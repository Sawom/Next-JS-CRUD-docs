# Next.js CRUD API Documentation

## 🚀 Project Overview
This is a **Next.js full-stack CRUD application** using **MongoDB** as the database and Next.js API Routes for backend services. The app allows users to **Create, Read, Update, and Delete (CRUD)** tasks.

---

## 📌 Setup and Installation

### **1️⃣ Clone the Repository**
```bash
git clone https://github.com/yourusername/nextjs-crud-app.git
cd nextjs-crud-app
```

### **2️⃣ Install Dependencies**
```bash
npm install
```

### **3️⃣ Setup Environment Variables**
Create a `.env.local` file in the root directory and add:
```
MONGODB_URI=mongodb+srv://yourusername:yourpassword@cluster.mongodb.net/nextjscrud
```

---

## 🏗️ **Database Connection**
We use **Mongoose** to connect to MongoDB.  
📌 File: `utils/db.js`
```javascript
import mongoose from "mongoose";

const MONGODB_URI = process.env.MONGODB_URI;

if (!MONGODB_URI) {
  throw new Error("Please define the MONGODB_URI in your .env file");
}

let cached = global.mongoose || { conn: null, promise: null };

async function connectDB() {
  if (cached.conn) return cached.conn;

  if (!cached.promise) {
    cached.promise = mongoose.connect(MONGODB_URI, {
      useNewUrlParser: true,
      useUnifiedTopology: true,
    }).then((mongoose) => mongoose);
  }
  cached.conn = await cached.promise;
  return cached.conn;
}

export default connectDB;
```

---

## 📁 **Task Schema (Model)**
📌 File: `models/Task.js`
```javascript
import mongoose from "mongoose";

const TaskSchema = new mongoose.Schema(
  {
    title: { type: String, required: true },
    description: { type: String },
  },
  { timestamps: true }
);

export default mongoose.models.Task || mongoose.model("Task", TaskSchema);
```

---

## 🌐 **API Endpoints**

### **1️⃣ Get All Tasks (READ)**
📌 **GET** `/api/tasks`
```javascript
export default async function handler(req, res) {
  await connectDB();
  if (req.method === "GET") {
    const tasks = await Task.find();
    return res.status(200).json(tasks);
  }
}
```
📌 **Response Example:**
```json
[
  {
    "_id": "65b7f1b1b1b1b1b1b1b1b1b1",
    "title": "Learn Next.js",
    "description": "Practice CRUD operations",
    "createdAt": "2025-03-15T14:00:00.000Z"
  }
]
```

---

### **2️⃣ Create a Task (POST)**
📌 **POST** `/api/tasks`
```javascript
export default async function handler(req, res) {
  await connectDB();
  if (req.method === "POST") {
    try {
      const { title, description } = req.body;
      const newTask = await Task.create({ title, description });
      return res.status(201).json(newTask);
    } catch (error) {
      return res.status(500).json({ error: "Task creation failed" });
    }
  }
}
```
📌 **Request Example:**
```json
{
  "title": "Build CRUD App",
  "description": "Use Next.js API Routes and MongoDB"
}
```

---

### **3️⃣ Update a Task (PUT)**
📌 **PUT** `/api/tasks/:id`
```javascript
export default async function handler(req, res) {
  await connectDB();
  const { id } = req.query;
  if (req.method === "PUT") {
    try {
      const { title, description } = req.body;
      const updatedTask = await Task.findByIdAndUpdate(
        id,
        { title, description },
        { new: true }
      );
      return res.status(200).json(updatedTask);
    } catch (error) {
      return res.status(500).json({ error: "Update failed" });
    }
  }
}
```
📌 **Request Example:**
```json
{
  "title": "Learn Full-Stack Development",
  "description": "Include authentication and authorization"
}
```

---

### **4️⃣ Delete a Task (DELETE)**
📌 **DELETE** `/api/tasks/:id`
```javascript
export default async function handler(req, res) {
  await connectDB();
  const { id } = req.query;
  if (req.method === "DELETE") {
    try {
      await Task.findByIdAndDelete(id);
      return res.status(200).json({ message: "Task deleted" });
    } catch (error) {
      return res.status(500).json({ error: "Delete failed" });
    }
  }
}
```
📌 **Response Example:**
```json
{
  "message": "Task deleted"
}
```

---

## 🎨 **Frontend (Fetching Tasks and UI)**
📌 File: `pages/index.js`
```javascript
import { useState, useEffect } from "react";
import axios from "axios";

export default function Home() {
  const [tasks, setTasks] = useState([]);
  const [title, setTitle] = useState("");

  useEffect(() => {
    fetchTasks();
  }, []);

  const fetchTasks = async () => {
    const { data } = await axios.get("/api/tasks");
    setTasks(data);
  };

  const addTask = async () => {
    await axios.post("/api/tasks", { title });
    setTitle("");
    fetchTasks();
  };

  const deleteTask = async (id) => {
    await axios.delete(`/api/tasks/${id}`);
    fetchTasks();
  };

  return (
    <div style={{ textAlign: "center", padding: "20px" }}>
      <h1>Task Manager</h1>
      <input
        type="text"
        placeholder="New Task"
        value={title}
        onChange={(e) => setTitle(e.target.value)}
      />
      <button onClick={addTask}>Add</button>
      <ul>
        {tasks.map((task) => (
          <li key={task._id}>
            {task.title}{" "}
            <button onClick={() => deleteTask(task._id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

## 🚀 **Running the Application**
```bash
npm run dev
```
Go to: **[http://localhost:3000](http://localhost:3000)**

---

## 📌 **Project Structure**
```
nextjs-crud-app/
│── models/
│   └── Task.js
│── pages/
│   ├── api/
│   │   ├── tasks/
│   │   │   ├── index.js
│   │   │   ├── [id].js
│── utils/
│   ├── db.js
│── .env.local
│── package.json
│── README.md
```

---

## 📢 **Conclusion**
🎉 You have successfully built a full-stack **Next.js CRUD** app with **MongoDB**! 🚀  
Feel free to enhance it by adding:
- ✅ Authentication (JWT or Firebase)
- ✅ Edit task functionality
- ✅ UI improvements with Tailwind CSS

---

  
📧 **Author:** *Abdur Rashid Sawom*  

Happy Coding! 🚀
