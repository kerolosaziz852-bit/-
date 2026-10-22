[Uploading import json.py…]()
# -import json
import os
import tkinter as tk
from tkinter import messagebox

DATA_FILE = "user_account.json"
SESSION_FILE = "user_session.json"


def save_account(username, password):
  data = {"username": username, "password": password}
  with open(DATA_FILE, "w", encoding="utf-8") as f:
    json.dump(data, f)


def load_account():
  if os.path.exists(DATA_FILE):
    with open(DATA_FILE, "r", encoding="utf-8") as f:
      return json.load(f)
  return None


def save_session(username):
  with open(SESSION_FILE, "w", encoding="utf-8") as f:
    json.dump({"username": username}, f)


def clear_session():
  if os.path.exists(SESSION_FILE):
    os.remove(SESSION_FILE)
  for widget in root.winfo_children():
    widget.destroy()
  create_login_screen()


def show_welcome_screen(username):
  for widget in root.winfo_children():
    widget.destroy()

  tk.Label(
      root, text=f"مرحباً بك مجدداً، {username}!", font=("Arial", 16, "bold")
  ).pack(pady=40)

  tk.Button(
      root,
      text="تسجيل الخروج",
      command=clear_session,
      bg="#ff4d4d",
      fg="white",
      font=("Arial", 12),
      width=15,
  ).pack(pady=10)


def create_forgot_password_screen():
  for widget in root.winfo_children():
    widget.destroy()

  tk.Label(root, text="استعادة كلمة المرور", font=("Arial", 18, "bold")).pack(
      pady=20
  )

  tk.Label(root, text="أدخل اسم المستخدم الخاص بك:", font=("Arial", 12)).pack()
  user_entry = tk.Entry(root, font=("Arial", 12))
  user_entry.pack(pady=5)

  tk.Label(root, text="كلمة المرور الجديدة:", font=("Arial", 12)).pack()
  new_pass_entry = tk.Entry(root, show="*", font=("Arial", 12))
  new_pass_entry.pack(pady=5)

  def handle_reset():
    username = user_entry.get().strip()
    new_password = new_pass_entry.get().strip()

    account = load_account()

    if not account:
      messagebox.showerror("خطأ", "لا يوجد أي حساب مسجل على هذا الجهاز.")
      return

    if username == account["username"]:
      if not new_password:
        messagebox.showerror("خطأ", "الرجاء إدخال كلمة المرور الجديدة.")
        return
      save_account(username, new_password)
      messagebox.showinfo("نجاح", "تم تغيير كلمة المرور بنجاح! يمكنك الدخول الآن.")
      create_login_screen()
    else:
      messagebox.showerror("خطأ", "اسم المستخدم غير مطابق للحساب المسجل.")

  tk.Button(
      root,
      text="تحديث كلمة المرور",
      command=handle_reset,
      bg="#FF9800",
      fg="white",
      font=("Arial", 12),
      width=15,
  ).pack(pady=15)

  tk.Button(
      root,
      text="العودة لتسجيل الدخول",
      command=create_login_screen,
      font=("Arial", 10),
      fg="blue",
      bd=0,
  ).pack(pady=5)


def create_register_screen():
  for widget in root.winfo_children():
    widget.destroy()

  tk.Label(
      root, text="تسجيل شلة الاحافي - حساب جديد", font=("Arial", 16, "bold")
  ).pack(pady=20)

  tk.Label(root, text="اسم المستخدم:", font=("Arial", 12)).pack()
  user_entry = tk.Entry(root, font=("Arial", 12))
  user_entry.pack(pady=5)

  tk.Label(root, text="كلمة المرور:", font=("Arial", 12)).pack()
  pass_entry = tk.Entry(root, show="*", font=("Arial", 12))
  pass_entry.pack(pady=5)

  def handle_register():
    username = user_entry.get().strip()
    password = pass_entry.get().strip()

    if not username or not password:
      messagebox.showerror("خطأ", "الرجاء ملء جميع الحقول")
      return

    save_account(username, password)
    save_session(username)
    messagebox.showinfo("نجاح", "تم إنشاء الحساب بنجاح!")
    show_welcome_screen(username)

  tk.Button(
      root,
      text="تسجيل",
      command=handle_register,
      bg="#4CAF50",
      fg="white",
      font=("Arial", 12),
      width=15,
  ).pack(pady=15)

  tk.Button(
      root,
      text="لديك حساب بالفعل؟ تسجيل الدخول",
      command=create_login_screen,
      font=("Arial", 10),
      fg="blue",
      bd=0,
  ).pack(pady=5)


def create_login_screen():
  for widget in root.winfo_children():
    widget.destroy()

  tk.Label(root, text="تسجيل شلة الاحافي", font=("Arial", 18, "bold")).pack(
      pady=20
  )

  tk.Label(root, text="اسم المستخدم:", font=("Arial", 12)).pack()
  user_entry = tk.Entry(root, font=("Arial", 12))
  user_entry.pack(pady=5)

  tk.Label(root, text="كلمة المرور:", font=("Arial", 12)).pack()
  pass_entry = tk.Entry(root, show="*", font=("Arial", 12))
  pass_entry.pack(pady=5)

  def handle_login():
    username = user_entry.get().strip()
    password = pass_entry.get().strip()

    account = load_account()

    if not account:
      messagebox.showerror(
          "خطأ", "لا يوجد حساب مسجل مسبقاً، قم بإنشاء حساب جديد أولاً."
      )
      return

    if username == account["username"] and password == account["password"]:
      save_session(username)
      show_welcome_screen(username)
    else:
      messagebox.showerror("خطأ", "اسم المستخدم أو كلمة المرور غير صحيحة")

  tk.Button(
      root,
      text="دخول",
      command=handle_login,
      bg="#2196F3",
      fg="white",
      font=("Arial", 12),
      width=15,
  ).pack(pady=10)

  tk.Button(
      root,
      text="هل نسيت كلمة المرور؟",
      command=create_forgot_password_screen,
      font=("Arial", 9),
      fg="red",
      bd=0,
  ).pack(pady=2)

  tk.Button(
      root,
      text="ليس لديك حساب؟ إنشاء حساب جديد",
      command=create_register_screen,
      font=("Arial", 10),
      fg="blue",
      bd=0,
  ).pack(pady=5)


# إعداد النافذة الرئيسية
root = tk.Tk()
root.title("تسجيل شلة الاحافي")
root.geometry("350x380")
root.resizable(False, False)

# التحقق مما إذا كان المستخدم قد سجل الدخول مسبقاً (جلسة نشطة)
if os.path.exists(SESSION_FILE):
  with open(SESSION_FILE, "r", encoding="utf-8") as f:
    session_data = json.load(f)
    show_welcome_screen(session_data["username"])
else:
  if load_account():
    create_login_screen()
  else:
    create_register_screen()

root.mainloop()
