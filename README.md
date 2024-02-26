SimpleTodo/
│
├── app/
│   ├── __init__.py
│   ├── routes.py
│   └── templates/
│       ├── base.html
│       ├── index.html
│       └── 404.html
│
├── static/
│   └── style.css
│
├── venv/  (虚拟环境，可选)
│
├── config.py
├── run.py
└── requirements.txt
安装依赖：
pip install flask
项目代码：
# app/__init__.py
from flask import Flask

app = Flask(__name__)

from app import routes
# app/routes.py
from flask import render_template, request, redirect, url_for, flash
from app import app

todos = []

@app.route('/')
def index():
    return render_template('index.html', todos=todos)

@app.route('/add', methods=['POST'])
def add():
    todo = request.form.get('todo')
    if todo:
        todos.append({'task': todo, 'done': False})
        flash('Task added successfully!', 'success')
    else:
        flash('Task cannot be empty!', 'danger')
    return redirect(url_for('index'))

@app.route('/complete/<int:todo_id>')
def complete(todo_id):
    if 0 <= todo_id < len(todos):
        todos[todo_id]['done'] = True
    return redirect(url_for('index'))

@app.route('/delete_completed')
def delete_completed():
    global todos
    todos = [todo for todo in todos if not todo['done']]
    return redirect(url_for('index'))

@app.errorhandler(404)
def page_not_found(e):
    return render_template('404.html'), 404
<!-- app/templates/base.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
    <title>SimpleTodo</title>
</head>
<body>
    <div class="container">
        {% block content %}{% endblock %}
    </div>
</body>
</html>
<!-- app/templates/index.html -->
{% extends 'base.html' %}

{% block content %}
    <h1>SimpleTodo</h1>
    
    <form action="{{ url_for('add') }}" method="post">
        <input type="text" name="todo" placeholder="Add a new task" required>
        <button type="submit">Add Task</button>
    </form>
    
    <ul>
        {% for index, todo in enumerate(todos) %}
            <li>
                {{ todo['task'] }}
                {% if not todo['done'] %}
                    <a href="{{ url_for('complete', todo_id=index) }}">Complete</a>
                {% endif %}
            </li>
        {% endfor %}
    </ul>
    
    <a href="{{ url_for('delete_completed') }}">Delete Completed Tasks</a>
{% endblock %}
<!-- app/templates/404.html -->
{% extends 'base.html' %}

{% block content %}
    <h1>404 - Not Found</h1>
    <p>The requested page does not exist.</p>
{% endblock %}
/* static/style.css */
body {
    font-family: 'Arial', sans-serif;
    background-color: #f4f4f4;
}

.container {
    max-width: 600px;
    margin: 50px auto;
    padding: 20px;
    background-color: #fff;
    border-radius: 5px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

form {
    margin-bottom: 20px;
}

ul {
    list-style-type: none;
    padding: 0;
}

li {
    border-bottom: 1px solid #ddd;
    padding: 10px 0;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

a {
    text-decoration: none;
    color: #007bff;
    cursor: pointer;
    margin-left: 10px;
}

a:hover {
    text-decoration: underline;
}

.flash {
    margin-bottom: 20px;
    padding: 10px;
    border-radius: 5px;
}

.success {
    background-color: #d4edda;
    color: #155724;
}

.danger {
    background-color: #f8d7da;
    color: #721c24;
}
运行项目：
在项目根目录下运行：
python run.py
