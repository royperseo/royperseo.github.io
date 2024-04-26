
# Flask con SQLAlchemy (MySQL)

## Flask básico

```bash
pip install mysqlclient
```

```python
from flask import Flask

app = Flask(__name__)

if __name__=='__main__':
    app.run(debug=True)
```

## Flask Blueprint

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
    return 'página de inicio'

@app.route('/guardar')
def save():
    return 'Elemento Guardado'

@app.route('/borrar')
def delete():
    return 'Elemento Borrado'

if __name__=='__main__':
    app.run(debug=True)
```

### Con blueprint

```python
# main.py

from flask import Flask
from rutas import rutas

app = Flask(__name__)
app.register_blueprint(rutas)

if __name__=='__main__':
    app.run(debug=True)
```

```python
# rutas.py
from flask import Blueprint

rutas = Blueprint('rutas', __name__)

@rutas.route('/')
def index():
    return 'página de inicio'

@rutas.route('/guardar')
def save():
    return 'Elemento Guardado'

@rutas.route('/borrar')
def delete():
    return 'Elemento Borrado'
```

## Flask SQLAlchemy

### Conexión a base de datos

```python
# main.py

from flask import Flask
from rutas import rutas

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql://user:password@server/db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

SQLAlchemy(app)

app.register_blueprint(rutas)

from db import db

with app.app_context():
    db.create_all()


if __name__=='__main__':
    app.run(debug=True)
```

```python
# db.py
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()
```

```python
# modelos.py

from db import db

class Contacts(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    fullname = db.Column(db.String(100))
    email = db.Column(db.String(100))
    phone = db.Column(db.String(100))
    
    def __init__(self, fullname, email, phone):
        self.fullname = fullname
        self.email = email
        self.phone = phone
          
```

### Guardar datos

```python
# rutas.py
from flask import Blueprint, render_template, request
from modelo import Contacts
from db import db

rutas = Blueprint('rutas', __name__)

@ruta.route('/new', methods=['POST'])
def save():
    fu = request.form['fullname']
    em = request.form['email']
    ph = request.form['phone']
    
    guardar = Contacts(fu,em,ph)
    db.session.add(guardar)
    db.session.commit()
    
    return "contacto guardado"
```

### Mostrar datos

```python
@ruta.routes('/')
def index():
    data = Contacts.query.all()
    return  render_template('index.html' data = data)
```

```jinja2
{% for item in data %}
    {{ item.fullname }}
    {{ item.email }}
    {{ item.phone }}
{% endfor %}
```

###  Rutas con blueprint

```python
# path desde el html (blueprint)
<a href="{{ url_for('rutas.update') }}">Update</a>
<a href="{{ url_for('rutas.delete') }}">Delete</a>
```

### Elimianar datos

```python
@ruta.routes('/delete/<id>')
def delete(id):
    data = Contacts.query.get(id)
    db.session.delete(data)
    db.session.commit()
    return  'eliminar datos'
```

```jinja2
{% for item in data %}
<a href="{{ url_for('rutas.delete', 'item.id') }}">Delete</a>
{% endfor %}
```

### Usar url_for

```python
return (url_for('rutas.index'))
```

### Actualizar datos

```jinja2
{% for item in data %}
<a href="{{ url_for('rutas.update', 'item.id') }}">Update</a>
{% endfor %}
```

```python
@ruta.routes('/update/<id>', methods=['GET', 'POST'])
def update(id):
    get_element = Contacts.query.get(id)
    return render_template('update.html' elementos=get_elements)
```

```html
<form action="/update/{{ elementos.id }}" method="POST">
    <input type="text" value="{{ elementos.fullname }}" name="fullname">
    <input type="text" value="{{ elementos.email }}" name="email">
    <input type="text" value="{{ elementos.phone }}" name="phone">
    <button>
        Actualizar
    </button>
</form>
```

```python
@ruta.routes('/update/<id>', methods=['GET', 'POST'])
def update(id):
    get_element = Contacts.query.get(id)
    if request.method == 'POST':    
    	get_element.fullname = request.form['fullname']
    	get_element.email = request.form['email']
    	get_element.phone = request.form['phone']
        db.session.commit()
        return "actualizando"
    return render_template('update.html' elementos=get_elements)
```

### Subir archivos a la base de datos

```html
<form action="/update" enctype="multipart/form-data" method="post">
    <input type="file" name="file">
    <button>
        Subir
    </button>
</form>
```

```python
# model
class Upload(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    filename = db.Column(db.String(50))
    data = db.Column(db.LargeBinary)
# ruta
@app.route('/',methods=['GET', 'POST'])
def index():
    if request.method == 'POST':
        file = request.files['file']
        upload = Upload(name=file.filename, data=file.read())
        db.session.add(upload)
        db.session.commit()
        return f'Uploaded: {file.filename}'
    return render_template('index.html')
```

### Descargar archivos de la base de datos

```python
# download part

from io import BytesIO

@app.route('/download/<file_id>')
def download(file_id):
    upload = Upload.query.filter_by(id=file_id).first()
    return send_file(BytesIO(upload.data), attachment_filename=upload.filename, as_attachment=True)
```

### Fechas

```python
import date

fecha = db.Column(db.Date)
```
