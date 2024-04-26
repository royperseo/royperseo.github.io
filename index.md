
# Flask con SQLAlchemy (MySQL)

| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Row 1    | Cell 2   | Cell 3   |
| Row 2    | Cell 5   | Cell 6   |
| Row 3    | Cell 8   | Cell 9   |

- asdafsf
- adssdffghfdf
    - jghjkkhghf
- hgjkhgjkk


### UUUUU


Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque bibendum rutrum lectus, sed condimentum elit mollis in. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Vivamus cursus dictum enim, nec porttitor velit. Suspendisse nec dolor id quam porta volutpat quis vitae metus. Morbi a enim ac lacus dictum imperdiet. Sed dictum magna nec fermentum tempus. Vestibulum a maximus elit.

Etiam purus dui, aliquam a aliquet nec, venenatis ac lectus. Nullam viverra turpis eget felis iaculis dictum. Curabitur id dignissim enim, eget mattis dolor. Maecenas placerat nulla varius, pulvinar nulla et, lacinia massa. Integer placerat turpis eget elit dictum, fringilla auctor purus dapibus. Fusce vel ante et nulla fringilla commodo et ut elit. Sed condimentum lectus ac urna bibendum, in dictum neque venenatis. Vestibulum non sapien tempus nisl porta eleifend non vitae ante. Integer diam magna, egestas sed cursus vitae, dictum cursus est. Cras ante ex, tempor at tempus id, ultrices et tortor.

Cras sed semper odio. Nunc at nulla sagittis leo aliquet ultrices vitae non quam. In id iaculis nunc. Aliquam vitae bibendum justo, vel sodales lorem. Cras imperdiet neque non tellus cursus, eget cursus elit eleifend. Suspendisse massa arcu, ultricies ac dignissim ac, fringilla ac sem. Praesent facilisis magna in libero consequat iaculis. Fusce rhoncus, odio sed efficitur porta, felis sapien vulputate enim, id pharetra nisi risus vel nunc. Phasellus et consectetur turpis, vel tempus magna. Donec ac sollicitudin nulla.

Mauris finibus finibus magna, et vestibulum odio feugiat ac. Nulla pellentesque lectus felis, quis varius eros accumsan hendrerit. Nulla quis posuere nulla, cursus consectetur nisi. Ut eu orci aliquam, maximus odio id, faucibus eros. Cras vel metus vel magna vehicula ultrices. Pellentesque venenatis urna in lacus pellentesque tempor. Aenean semper quis quam cursus gravida. Curabitur nec risus quis diam convallis cursus. Maecenas nec convallis nunc. Integer convallis lectus porttitor orci euismod, et volutpat turpis cursus. Cras egestas interdum suscipit. Proin vehicula dolor a magna sodales, at bibendum sem mollis. Morbi a sapien at mauris tristique venenatis. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. In blandit libero a massa elementum imperdiet. Duis placerat nulla a mauris pretium, a pharetra leo tempus.

In convallis nulla at nibh auctor vehicula. Sed suscipit convallis lacus et gravida. Morbi commodo, dui facilisis ultrices elementum, ligula diam congue turpis, tincidunt pellentesque tellus erat ac eros. Cras auctor tortor ac iaculis accumsan. Phasellus ligula orci, consectetur in consectetur quis, facilisis et nisi. Vestibulum gravida pellentesque tellus, placerat pulvinar est. Pellentesque in quam iaculis, venenatis nulla in, gravida est. Class aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos. In volutpat augue et placerat ultricies. Nullam lobortis rhoncus nulla, at placerat sapien sollicitudin non. Nam porta ligula non massa iaculis aliquet. Pellentesque at condimentum ex.

#### OOOOOOOOOOO

##### )9999999999dfskfjdskjf

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
