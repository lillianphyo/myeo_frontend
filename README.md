### 📘 `README.md` for Frontend (Svelte)

```markdown
# 🎓 Student Grading Dashboard – Frontend

This is the frontend application of the **Student Grading Dashboard**, built with **Svelte** and **JavaScript**. It interacts with a Flask-based backend and a MySQL database to manage and display course, student, and grading information.

---

## 🛠️ Tech Stack

- **Frontend Framework:** [Svelte](https://svelte.dev)
- **Language:** JavaScript
- **Backend:** [Flask (Python)](https://flask.palletsprojects.com)
- **Database:** MySQL
- **API Communication:** RESTful APIs (via `fetch`)

---

## 📂 Project Structure

├── index.html
├── package.json
├── package-lock.json
├── README.md
├── src
│   ├── App.svelte
│   ├── components
│   │   ├── AddCourseModal.svelte
│   │   ├── CourseList.svelte
│   │   ├── Navbar.svelte
│   │   ├── RegisterStudentModal.svelte
│   │   ├── UploadGradesModal.svelte
│   │   └── ViewGrades.svelte
│   ├── main.js
│   ├── stores
│   │   └── auth.js
│   └── styles
│       └── global.css
└── vite.config.js

---

## 🚀 Getting Started

### 1. Prerequisites

- Node.js (v18 or later recommended)
- npm or yarn

### 2. Install Dependencies

```bash
cd frontend
npm install
```

### 3. Run Development Server

```bash
npm run dev
```

The app should now be running at:  
👉 `http://localhost:5173`

### 4. API Integration

Ensure the Flask backend is running (typically at `http://localhost:5000`) and endpoints like `/api/students`, `/api/grades`, etc., are accessible.

You may need to update base API URLs in your `.env` or in the Svelte `fetch` calls depending on your environment.

---

## 🔗 Backend Repository

The backend Flask server is located at:  
➡️ [`/backend`](https://github.com/lillianphyo/myeo-backend.git)

---

## 🛠️ Build for Production

```bash
npm run build
```

The static site will be compiled to the `dist/` folder, ready to be served with any static hosting or integrated into the Flask app using something like `Flask-Static-Digest`.

---

## 📦 Deployment Options

You can deploy the frontend using:
- **Vercel**
- **Netlify**
- **Static export served by Flask**
- **GitHub Pages** (if using a static export)

---

## 🙌 Author

**Lillian Phyo**  
[https://lillianphyo.net](https://lillianphyo.net)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).
```
