# Parcial2_Basedatos_zarampion
Descargue la fuente de datos relacionada con vacunación contra el sarampión en niños entre 12-23 meses en Panamá del Word Bank y diseñe un API tipo REST de sólo-lectura que manipule estos datos.
Achivo1
import sqlite3
DATABASE_NAME = "sarampion.db"


def obtener_db():
    conn = sqlite3.connect(DATABASE_NAME)
    return conn


def create_tables():
    tables = [
        """CREATE TABLE IF NOT EXISTS datos(
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                name TEXT NOT NULL,
                code TEXT NOT NULL,
                tiempo INTEGER,
                tCode TEXT NOT NULL,
                porcentaje INTEGER
            )
            """
    ]
    db = obtener_db()
    cursor = db.cursor()
    for table in tables:
        cursor.execute(table)
        
        #crear un archivo2 para el controlador
from bbdd import obtener_db


def insertar_dato(name, code, tiempo, tcode, porcentaje):
    db = obtener_db()
    cursor = db.cursor()
    sentencia = "INSERT INTO datos(name, code, tiempo, tcode, porcentaje) VALUES (?, ?, ?, ?, ?)"
    cursor.execute(sentencia, [name, code, tiempo, tcode, porcentaje])
    db.commit()
    return True

def obtener_datos():
    db = obtener_db()
    cursor = db.cursor()
    query = "SELECT id, name, code, tiempo, tcode, porcentaje FROM datos"
    cursor.execute(query)
    return cursor.fetchall()

def eliminar_dato(id):
    db = obtener_db()
    cursor = db.cursor()
    sentencia = "DELETE FROM datos WHERE id = ?"
    cursor.execute(sentencia, [id])
    db.commit()
    return True

def obtener_dato_por_id(id):
    db = obtener_db()
    cursor = db.cursor()
    sentencia = "SELECT id, name, code, tiempo, tcode, porcentaje FROM datos WHERE id = ?"
    cursor.execute(sentencia, [id])
    return cursor.fetchone()

def actualizar_dato(id, name, code, tiempo, tcode, porcentaje):
    db = obtener_db()
    cursor = db.cursor()
    sentencia = "UPDATE datos SET name = ?, code = ?, tiempo = ?, tcode = ?, porcentaje= ? WHERE id = ?"
    cursor.execute(sentencia, [name, code, tiempo, tcode, porcentaje, id])
    db.commit()
    return True
    
    #archivo3 main
    from flask import Flask, render_template, request, redirect, flash, jsonify
import controlador_datos
import sqlite3 as sql
from bbdd import create_tables, obtener_db

app = Flask(__name__)


@app.route("/tabla")
def datos():
    datos = controlador_datos.obtener_datos()
    return jsonify(datos)


@app.route("/dato", methods=["GET"])
def guardar_dato():
    data_details = request.get_json()
    name = data_details["name"]
    code = data_details["code"]
    tiempo = data_details["tiempo"]
    tcode = data_details["tcode"]
    porcentaje = data_details["porcentaje"]

    resultado = controlador_datos.insertar_dato(name, code, tiempo, tcode, porcentaje)
    return redirect("/datos")
    return jsonify(resultado)




@app.route("/dato", methods=["DELETE"])
def eliminar_dato():
    resultado = controlador_datos.eliminar_dato(data_details["id"])
    return jsonify(resultado)


@app.route("/dato/<int:id>")
def editar_dato(id):
    # Obtener el dato por ID
    dato = controlador_datos.obtener_dato_por_id(id)
    return jsonify(dato)


@app.route("/dato", methods=["PUT"])
def actualizar_dato():
    data_details = request.get_json()
    id = data_details["id"]
    name = data_details["name"]
    code = data_details["code"]
    tiempo = data_details["tiempo"]
    tcode = data_details["tcode"]
    porcentaje = data_details["porcentaje"]
    resultado = controlador_datos.actualizar_dato(name, code, tiempo, tcode, porcentaje, id)
    return jsonify(resultado)

@app.route('/')
def tabla():

    con = sql.connect("sarampion.db")
    con.row_factory = sql.Row
   
    cur = con.cursor()
    cur.execute("select * from datos")
   
    rows = cur.fetchall();
    return render_template("tabla.html",rows = rows)


# Iniciar el servidor
if __name__ == "__main__":
    create_tables()
 
    app.run(port=8046, debug=False)
        
