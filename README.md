from flask import Flask, render_template, request, redirect, url_for
from flask_mysqldb import MySQL

app = Flask(__name__)

# Configure MySQL
app.config['MYSQL_HOST'] = 'your_mysql_host'
app.config['MYSQL_USER'] = 'your_mysql_user'
app.config['MYSQL_PASSWORD'] = 'your_mysql_password'
app.config['MYSQL_DB'] = 'your_mysql_database'

mysql = MySQL(app)

# Home route
@app.route('/')
def home():
    # Fetch data from the database
    cur = mysql.connection.cursor()
    cur.execute("SELECT * FROM children")
    children = cur.fetchall()
    cur.close()

    return render_template('index.html', children=children)

# Donation route
@app.route('/donate', methods=['POST'])
def donate():
    if request.method == 'POST':
        # Get donation information from the form
        child_id = request.form['child_id']
        amount = request.form['amount']

        # Insert donation into the database
        cur = mysql.connection.cursor()
        cur.execute("INSERT INTO donations (child_id, amount) VALUES (%s, %s)", (child_id, amount))
        mysql.connection.commit()
        cur.close()

        return redirect(url_for('home'))

if __name__ == '__main__':
    app.run(debug=True)
