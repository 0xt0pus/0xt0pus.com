---
author: "0xt0pus"
title: "Qualifier Web security (El Banco Amarillo) - CyberHackathon'23"
date: "2023-09-09"
description: ""
tags: ["CyberHackathon'23", "Web", "CTF-Walkthrough"]
ShowToc: true
TocOpen: false
---


The web security challenge was vulnerable to race condition.

The following was the given website.

![](/writeups/Amarillo-CTF/1.png)

The following were the user details with IDs and funds.

![](/writeups/Amarillo-CTF/2.png)


the /dump path has a comment, which reveals the source code.

![](/writeups/Amarillo-CTF/3.png)

The following part of the code was vulnerable to Race Condition. A race window exists in the if condition. We can send multiple request parallelly, it will bypass the if condition before the deduction of funds.

![](/writeups/Amarillo-CTF/4.png)

The flag can only be obtained by making the funds more than 405. Which by default are 405 and there is no other way to increase the funds.

![](/writeups/Amarillo-CTF/5.png)

i sent a request to /transfer endpoint.

![](/writeups/Amarillo-CTF/6.png)

I captured the request on burp, and grouped afew requests with same parameter values.

![](/writeups/Amarillo-CTF/7.png)

I sent all the requests in parallel.

![](/writeups/Amarillo-CTF/8.png)

I did it a few times untill it works. The following is the result after a few tries. Where made the value more than 405.

![](/writeups/Amarillo-CTF/9.png)

I bought flag with user id=2.

![](/writeups/Amarillo-CTF/10.png)

It gives me the flag as shown below.

![](/writeups/Amarillo-CTF/11.png)

The full source code:


```python
from flask import Flask, render_template, request, redirect, url_for, flash
import sqlite3

app = Flask(__name__)
app.secret_key = 'secretkey'

DATABASE = 'users.db'
FLAG = 'fake flag'


def create_db():
    # Create the database tables if they don't exist
    conn = sqlite3.connect(DATABASE)
    cursor = conn.cursor()

    # Create the 'users' table if it doesn't exist
    cursor.execute('''
            CREATE TABLE IF NOT EXISTS users (
                id INTEGER PRIMARY KEY,
                funds INTEGER
            )
        ''')

    # Insert initial data if the table is empty
    cursor.execute('SELECT COUNT(*) FROM users')
    count = cursor.fetchone()[0]

    if count == 0:
        cursor.execute('''
                INSERT INTO users (id, funds) VALUES
                (1, 350),
                (2, 55)
            ''')

    conn.commit()
    conn.close()


app.before_request_funcs = [(None, create_db())]


@app.route('/')
def index():
    return render_template('index.html')


@app.route('/dump')
def dump_users():
    try:
        conn = sqlite3.connect(DATABASE)
        cursor = conn.cursor()
        cursor.execute("SELECT id, funds FROM users")
        users = cursor.fetchall()
        return render_template('dump.html', users=users)
    except sqlite3.Error as e:
        flash('Error: ' + str(e), 'error')
    finally:
        conn.close()
    return redirect(url_for('index'))


@app.route('/reset')
def reset_funds():
    try:
        conn = sqlite3.connect(DATABASE)
        cursor = conn.cursor()
        cursor.execute("UPDATE users SET funds = 350 WHERE id = 1")
        cursor.execute("UPDATE users SET funds = 55 WHERE id = 2")
        conn.commit()
        flash('Funds updated!', 'success')
    except sqlite3.Error as e:
        flash('Error: ' + str(e), 'error')
    finally:
        conn.close()
    return redirect(url_for('index'))


@app.route('/buy_flag', methods=['POST'])
def buy_flag():
    user_id = request.form.get('user_id')
    try:
        conn = sqlite3.connect(DATABASE)
        cursor = conn.cursor()
        cursor.execute("SELECT funds FROM users WHERE id = ?", (user_id,))
        funds = cursor.fetchone()[0]
        if funds > 405:
            return FLAG
        else:
            flash('Insufficient funds!', 'error')
    except sqlite3.Error as e:
        flash('Error: ' + str(e), 'error')
    finally:
        conn.close()
    return redirect(url_for('index'))


@app.route('/transfer')
def transfer_funds():
    to_user_id = request.args.get('to_user_id')
    from_user_id = request.args.get('from_user_id')
    amount = request.args.get('amount')

    try:
        conn = sqlite3.connect(DATABASE)
        cursor = conn.cursor()
        cursor.execute("SELECT funds FROM users WHERE id = ?", (from_user_id,))
        from_user_funds = cursor.fetchone()[0]

        if (
                to_user_id != from_user_id
                and 0 < int(amount) <= 247
                and from_user_funds >= int(amount)
        ):
            cursor.execute("UPDATE users SET funds = funds - ? WHERE id = ?", (amount, from_user_id))
            cursor.execute("UPDATE users SET funds = funds + ? WHERE id = ?", (amount, to_user_id))
            conn.commit()
            flash('Funds transferred!', 'success')
        else:
            flash('Invalid transfer request!', 'error')
    except sqlite3.Error as e:
        flash('Error: ' + str(e), 'error')
    finally:
        conn.close()
    return redirect(url_for('index'))


if __name__ == '__main__':
    app.run(host='0.0.0.0')
```

Enjoy :)

