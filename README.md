Nikita, [30.05.2025 21:42]
def load_materials():
    try:
        cursor = db_connection.cursor()
        cursor.execute("""
            SELECT m.id, m.name, mt.type, m.chena_edinit, 
                   m.sclad_col, m.min_col, m.col_ypakovka, m.edin_izmer
            FROM Materials m
            JOIN Material_type mt ON m.Material_type_id = mt.id
       """)

        # Очистка таблицы
        for row in tree.get_children():
            tree.delete(row)

        # Заполнение данными
        for row in cursor:
            tree.insert("", tk.END, values=row)

        cursor.close()
    except mysql.connector.Error as err:
        messagebox.showerror("Ошибка", f"Ошибка загрузки материалов: {err}")

Nikita, [30.05.2025 21:43]
def save_material(data, edit_id=None):
    try:
        cursor = db_connection.cursor()
        if edit_id is None:
            query = """
                INSERT INTO Materials (name, type, chena_edinit, sclad_col, 
                                      min_col, col_ypakovka, edin_izmer, Material_type_id)
                VALUES (%s, %s, %s, %s, %s, %s, %s, %s)"""
            
            cursor.execute(query, data)
        else:
            query = """
                UPDATE Materials 
                SET name=%s, type=%s, chena_edinit=%s, sclad_col=%s,
                    min_col=%s, col_ypakovka=%s, edin_izmer=%s, Material_type_id=%s
                WHERE id=%s"""
            
            cursor.execute(query, data + (edit_id,))

        db_connection.commit()
        cursor.close()
        return True
    except mysql.connector.Error as err:
        messagebox.showerror("Ошибка", f"Ошибка сохранения: {err}")
        return False

Nikita, [30.05.2025 21:44]
if edit_id is not None:
        try:
            cursor = db_connection.cursor()
            cursor.execute("SELECT * FROM Materials WHERE id = %s", (edit_id,))
            material = cursor.fetchone()
            cursor.close()

            if material:
                name_entry.insert(0, material[1])
                price_entry.insert(0, str(material[3]))
                stock_entry.insert(0, str(material[4]))
                min_entry.insert(0, str(material[5]))
                pack_entry.insert(0, str(material[6]))
                unit_combobox.set(material[7])
        except mysql.connector.Error as err:
            messagebox.showerror("Ошибка", f"Ошибка загрузки материала: {err}")

    # Загрузка типов материалов
    type_combobox['values'] = [t[1] for t in material_types]
    if material_types:
        type_combobox.current(0)

Nikita, [30.05.2025 21:45]
def on_save():
        try:
            material_type_id = material_types[type_combobox.current()][0]
            data = (name_entry.get(),
                type_combobox.get(),
                Decimal(price_entry.get()),
                Decimal(stock_entry.get()),
                Decimal(min_entry.get()),
                Decimal(pack_entry.get()),
                unit_combobox.get(),
                material_type_id
            )

            if save_material(data, edit_id):
                form.destroy()
                load_materials()

        except ValueError:
            messagebox.showerror("Ошибка", "Проверьте правильность ввода числовых значений!")

    tk.Button(form, text="Сохранить", command=on_save).grid(row=7, column=0, columnspan=2, pady=10)
    tk.Button(form, text="Отмена", command=form.destroy).grid(row=8, column=0, columnspan=2)

Nikita, [30.05.2025 21:45]
def connect_db():
    try:
        conn = mysql.connector.connect(
            host="localhost",
            user="root",
            password="1234",
            database="obraz_plus"
        )
        return conn
    except mysql.connector.Error as err:
        messagebox.showerror("Ошибка", f"Не удалось подключиться к MySQL: {err}")
        return None

Nikita, [30.05.2025 21:46]
root.iconbitmap("Образ плюс.ico")

img = PhotoImage(file="Образ плюс.png")
small_img = img.subsample(5, 5)
label = tk.Label(root, image=small_img)
label.pack()

Nikita, [30.05.2025 21:58]
root.withdraw() 
command=lambda: [edit_window.destroy(), root.deiconify()]

Nikita, [30.05.2025 22:08]
columns =["id", "farvor", "You"]

tree = ttk.Treeview(root, columns=columns, show="headings", selectmode="browse")

tree.heading("id", text="ID")
tree.heading("farvor", text="Имя")
tree.heading("You", text="Возраст")

 Настройка столбцов
tree.column("id", width=50, anchor=tk.CENTER)
tree.column("farvor", width=150)
tree.column("You", width=80, anchor=tk.CENTER)

пРОВЕРКА ВАЛИДАЦИИ

ef check_data():
    # Получаем данные из полей
    text_value = entry1.get().strip()
    number_value = entry2.get().strip()
    float_value = entry3.get().strip()

    errors = []  # Список ошибок

    # 1. Проверка первого поля (только буквы, без цифр)
    if not text_value.isalpha():
        errors.append("◉ Поле 1: Допустимы только буквы (без цифр и символов)")

    # 2. Проверка второго поля (целое число)
    if not number_value.isdigit():
        errors.append("◉ Поле 2: Введите целое число (например: 5, 100)")

    # 3. Проверка третьего поля (число с запятой)
    try:
        # Заменяем запятую на точку и пробуем преобразовать
        float(float_value.replace(",", "."))
    except ValueError:
        errors.append("◉ Поле 3: Введите число с запятой (например: 0,5 или 3,14)")

    # Выводим ошибки или успех
    if errors:
        messagebox.showerror("Ошибка", "\n".join(errors))
    else:
        messagebox.showinfo("✅ Успех", "Данные введены верно!")

# Создаем окно
root = tk.Tk()
root.title("Проверка данных")
root.geometry("300x250")

# Поле 1: Только буквы
ttk.Label(root, text="Только буквы:").pack(pady=5)
entry1 = ttk.Entry(root)
entry1.pack()

# Поле 2: Только целые числа
ttk.Label(root, text="Только целые числа:").pack(pady=5)
entry2 = ttk.Entry(root)
entry2.pack()

# Поле 3: Числа с запятой
ttk.Label(root, text="Числа с запятой (0,5):").pack(pady=5)
entry3 = ttk.Entry(root)
entry3.pack()

# Кнопка проверки
ttk.Button(root, text="Проверить", command=check_data).pack(pady=10)

root.mainloop()
