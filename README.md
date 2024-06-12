# Web-Application-Firewall-WAF-

from flask import Flask, request, render_template_string
import re
import sqlite3

app = Flask(__name__)

# Simple SQL injection protection
def sanitize_input(user_input):
    return re.sub(r'[^a-zA-Z0-9]', '', user_input)

@app.route('/')
def home():
    return "Web Application Firewall is running."

@app.route('/search', methods=['GET'])
def search():
    query = sanitize_input(request.args.get('q', ''))
    conn = sqlite3.connect('example.db')
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM data WHERE info LIKE ?", ('%' + query + '%',))
    results = cursor.fetchall()
    conn.close()
    return render_template_string("<p>{{ results }}</p>", results=results)

if __name__ == "__main__":
    app.run(debug=True)

# setup_db.py
import sqlite3

def setup_database():
    conn = sqlite3.connect('example.db')
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS data (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            info TEXT NOT NULL
        )
    ''')
    cursor.executemany('''
        INSERT INTO data (info) VALUES (?)
    ''', [('example1',), ('example2',), ('example3',)])
    conn.commit()
    conn.close()

if __name__ == "__main__":
    setup_database()
    print("Database setup complete.")
