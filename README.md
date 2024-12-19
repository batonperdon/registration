Импортирование библиотек

import tkinter as tk
from tkinter import messagebox
import sqlite3


 tkinter: Библиотека для создания графического пользовательского интерфейса (GUI) в Python.

 messagebox: Подмодуль tkinter, который позволяет отображать всплывающие окна с сообщениями.

 sqlite3: Библиотека для работы с SQLite, встроенной базой данных в Python.

Создание базы данных и таблицы пользователей

def create_db():
    conn = sqlite3.connect('users.db')
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS users (
            id INTEGER PRIMARY KEY,
            username TEXT UNIQUE,
            password TEXT
        )
    ''')
    conn.commit()
    conn.close()


 create_db(): Функция, которая создает базу данных и таблицу пользователей, если она еще не существует.

   sqlite3.connect('users.db'): Создает или открывает базу данных users.db.

   cursor.execute(...): Выполняет SQL-запрос для создания таблицы users с тремя полями: id, username и password.

   conn.commit(): Сохраняет изменения в базе данных.

   conn.close(): Закрывает соединение с базой данных.

Регистрация нового пользователя

def register_user(username, password):
    conn = sqlite3.connect('users.db')
    cursor = conn.cursor()
    try:
        cursor.execute('INSERT INTO users (username, password) VALUES (?, ?)', (username, password))
        conn.commit()
        messagebox.showinfo("Успех", "Пользователь успешно зарегистрирован!")
    except sqlite3.IntegrityError:
        messagebox.showerror("Ошибка", "Пользователь с таким именем уже существует.")
    finally:
        conn.close()


 register_user(username, password): Функция для регистрации нового пользователя.

   Использует INSERT INTO для добавления нового пользователя в таблицу users.

   Если имя пользователя уже существует (вызывает ошибку IntegrityError), выводится сообщение об ошибке.

   Если регистрация успешна, выводится сообщение об успехе.

Авторизация пользователя

def authorize_user(username, password):
    conn = sqlite3.connect('users.db')
    cursor = conn.cursor()
    cursor.execute('SELECT * FROM users WHERE username=? AND password=?', (username, password))
    user = cursor.fetchone()
    conn.close()

    if user:
        messagebox.showinfo("Успех", "Вы успешно авторизованы!")
    else:
        messagebox.showerror("Ошибка", "Неверный логин или пароль.")


 authorize_user(username, password): Функция для авторизации пользователя.

   Выполняет SQL-запрос для поиска пользователя с указанным именем и паролем.

   Если пользователь найден, выводится сообщение об успешной авторизации; если нет — сообщение об ошибке.

Окно регистрации

def open_registration_window():
    reg_window = tk.Toplevel(root)
    reg_window.title("Регистрация")

    tk.Label(reg_window, text="Логин").pack(pady=5)
    username_entry = tk.Entry(reg_window)
    username_entry.pack(pady=5)

    tk.Label(reg_window, text="Пароль").pack(pady=5)
    password_entry = tk.Entry(reg_window, show='*')
    password_entry.pack(pady=5)

    tk.Button(reg_window, text="Регистрация",
              command=lambda: register_user(username_entry.get(), password_entry.get())).pack(pady=10)


 open_registration_window(): Функция для открытия окна регистрации.

   Создает новое окно с полями для ввода логина и пароля.

   Кнопка "Регистрация" вызывает функцию register_user, передавая введенные данные.

Основное окно приложения

root = tk.Tk()
root.title("Авторизация")

create_db()

tk.Label(root, text="Логин").pack(pady=5)
entry_username = tk.Entry(root)
entry_username.pack(pady=5)

tk.Label(root, text="Пароль").pack(pady=5)
entry_password = tk.Entry(root, show='*')
entry_password.pack(pady=5)
