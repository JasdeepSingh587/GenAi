# Code of Extracting a file and getting answer from the user

from fastapi import FastAPI, File, UploadFile, Form
from fastapi.responses import JSONResponse
from file_utils import extract_text
from rag_pipeline import process_document, answer_question

import os

app = FastAPI()

# 📤 Upload and process PDF or Word
@app.post("/upload/")
async def upload_file(file: UploadFile = File(...)):
    # Save uploaded file temporarily
    os.makedirs("temp_files", exist_ok=True)
    file_path = f"temp_files/{file.filename}"
    with open(file_path, "wb") as f:
        f.write(await file.read())

    # Extract text and process it
    try:
        text = extract_text(file_path)
        process_document(text, file.filename)
        return {"status": "Document processed successfully"}
    except Exception as e:
        return JSONResponse(status_code=500, content={"error": str(e)})

# ❓ Handle Question
@app.post("/query/")
async def query(question: str = Form(...)):
    try:
        answer = answer_question(question)
        return {"answer": answer}
    except Exception as e:
        return JSONResponse(status_code=500, content={"error": str(e)})


# Explanation of code Step by step

from fastapi import FastAPI, File, UploadFile, Form
from fastapi.responses import JSONResponse
from file_utils import extract_text
from rag_pipeline import process_document, answer_question

import os

app = FastAPI()

# 📤 Upload and process PDF or Word
@app.post("/upload/")
async def upload_file(file: UploadFile = File(...)):
    # Save uploaded file temporarily
    os.makedirs("temp_files", exist_ok=True)
    file_path = f"temp_files/{file.filename}"
    with open(file_path, "wb") as f:
        f.write(await file.read())

    # Extract text and process it
    try:
        text = extract_text(file_path)
        process_document(text, file.filename)
        return {"status": "Document processed successfully"}
    except Exception as e:
        return JSONResponse(status_code=500, content={"error": str(e)})

# ❓ Handle Question
@app.post("/query/")
async def query(question: str = Form(...)):
    try:
        answer = answer_question(question)
        return {"answer": answer}
    except Exception as e:
        return JSONResponse(status_code=500, content={"error": str(e)})





#Explanation of code step by step 
Great question! Let's break this down clearly:

```python
async def upload_file(file: UploadFile = File(...)):
```

### ✅ 1. **Function Definition**

* `upload_file` is an **asynchronous function** (`async def`) that handles file uploads in a FastAPI app.
* It gets called when a user hits the `/upload/` route with a POST request and a file.

---

### ✅ 2. **Parameter Breakdown**

```python
file: UploadFile = File(...)
```

This is the core part you're asking about. Let's understand each piece:

---

### 🔹 `file: UploadFile`

* This means the incoming parameter named `file` will be of type `UploadFile`.
* `UploadFile` is a special class in FastAPI that handles **file uploads efficiently**.

  * It gives you methods like `.read()`, `.filename`, `.content_type`, etc.
  * It’s more memory-efficient than reading the file as raw bytes.

---

### 🔹 `= File(...)`

* `File(...)` is a FastAPI dependency that **tells FastAPI to expect a file upload**.
* `...` (Ellipsis) means: this field is **required**.

  * Same as saying: "You must include this file in the request."

---

### 🧠 So what does `File(...)` actually do?

It does two main things:

1. **Tells FastAPI to expect the input from a `multipart/form-data` request** (used for uploading files via forms).
2. **Extracts and validates the uploaded file**, and passes it to the parameter (`file`) as an instance of `UploadFile`.

---

### 💡 Example from a Client (like Postman or an HTML Form):

If you're using a form to upload a file:

```html
<form action="/upload/" method="post" enctype="multipart/form-data">
  <input type="file" name="file" />
  <button type="submit">Upload</button>
</form>
```

That form sends the file with the field name `file`, which matches the parameter name in:

```python
async def upload_file(file: UploadFile = File(...))
```

---

### ✅ Summary:

| Component        | Purpose                                                                             |
| ---------------- | ----------------------------------------------------------------------------------- |
| `UploadFile`     | A FastAPI class to handle uploaded files with extra methods and info.               |
| `File(...)`      | Declares this input comes from an uploaded file in a `multipart/form-data` request. |
| `...` (ellipsis) | Marks the file as **required**.                                                     |

Let me know if you'd like to see how it behaves when uploading multiple files or handling optional uploads!


## code 
  os.makedirs("temp_files", exist_ok=True)
    file_path = f"temp_files/{file.filename}"
    with open(file_path, "wb") as f:
        f.write(await file.read())

## explanation
Absolutely! Let's deeply understand each of these three lines, step by step.

---

## 🔍 Code Block:

```python
os.makedirs("temp_files", exist_ok=True)
file_path = f"temp_files/{file.filename}"
with open(file_path, "wb") as f:
    f.write(await file.read())
```

---

### ✅ **1. `os.makedirs("temp_files", exist_ok=True)`**

#### 🔧 What this does:

* Creates a directory (folder) named `temp_files` in your current working directory **if it doesn’t already exist**.

#### 🔍 Breakdown:

* `os` is the standard Python module for interacting with the operating system (file system, environment, etc).
* `makedirs()` is used to **create folders recursively**, meaning:

  * If you gave `"temp_files/subdir"`, it would create both if needed.

#### 🧠 Why this is needed:

* When a user uploads a file, we want to **save it to the server**.
* But we can't save a file in a folder that doesn’t exist.
* This ensures the `temp_files` folder exists, so we can safely write the uploaded file into it.

#### ⚠️ `exist_ok=True`:

* Prevents an error if the folder already exists.
* Without this, Python would raise a `FileExistsError`.

---

### ✅ **2. `file_path = f"temp_files/{file.filename}"`**

#### 🔧 What this does:

* Builds a **full file path** to where the uploaded file will be saved.

#### 🔍 Breakdown:

* `file.filename` gives the **original name of the uploaded file** (e.g., `"resume.pdf"`).
* `f"temp_files/{file.filename}"` is a Python **f-string**, which dynamically builds a string.
* So if the user uploaded `"notes.docx"`, this becomes:

  ```python
  file_path = "temp_files/notes.docx"
  ```

#### 🧠 Why this is needed:

* We need to know **where to save the uploaded file**.
* This line generates the full path to save it in the `temp_files` folder using its original name.

---

### ✅ **3. `with open(file_path, "wb") as f:`**

#### 🔧 What this does:

* Opens the file at the path `file_path` in **write-binary mode (`"wb"`)**.

#### 🔍 Breakdown:

* `"wb"` = **Write Binary**

  * Used because uploaded files are in **binary format** (not plain text).
  * This mode is necessary for all file types, including PDFs and Word docs.
* `with` = **context manager**:

  * Ensures the file is **properly closed** after writing, even if an error occurs.
* `as f:` binds the opened file to the variable `f`.

#### 🧠 Why this is needed:

* We need to save the uploaded file **onto the disk**.
* Opening it in write-binary mode is the standard way to save any uploaded file.

---

### ✅ **4. `f.write(await file.read())`**

#### 🔧 What this does:

* Reads the uploaded file and writes its content to disk.

#### 🔍 Breakdown:

* `await file.read()`:

  * Asynchronously reads the entire content of the uploaded file.
  * Since FastAPI is async-compatible, using `await` avoids blocking the event loop.
* `f.write(...)`:

  * Writes the binary content to the open file `f`.

#### 🧠 Why this is needed:

* Without reading the uploaded file and writing it to disk, you can’t process it.
* This line **physically saves** the uploaded file into the server folder.

---

## 📦 Real-Life Example:

Assume someone uploads a file named `"report.pdf"`:

| Step          | Output                                              |
| ------------- | --------------------------------------------------- |
| Create folder | Creates `temp_files/` if not already there          |
| Build path    | `file_path = "temp_files/report.pdf"`               |
| Open + write  | Writes uploaded file into `"temp_files/report.pdf"` |

---

## 🧠 Summary Table:

| Line                         | Purpose                  | Why it's Important                   |
| ---------------------------- | ------------------------ | ------------------------------------ |
| `os.makedirs(...)`           | Create folder if missing | Prevent errors while saving the file |
| `file_path = ...`            | Build save path          | Know where to save the file          |
| `with open(...)`             | Open file to write       | Write safely using context manager   |
| `f.write(await file.read())` | Save file content        | Actually stores the uploaded file    |

---

Let me know if you want to see how to **read it back later**, or how to **handle multiple files** too!



