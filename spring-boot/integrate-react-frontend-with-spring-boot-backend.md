A step-by-step guide to build a simple full-stack application using React (frontend) talking to a Spring Boot REST API (backend). The tutorial covers project setup, CORS/proxy configuration, sample code for both sides, running locally, and packaging the React build inside Spring Boot for production.

---

## Table of Contents

1. Overview
2. Prerequisites
3. Backend (Spring Boot)

   * Create project
   * Dependencies
   * Simple domain + controller
   * CORS and security notes
   * Run backend
4. Frontend (React)

   * Create project
   * Axios and API helper
   * Example component
   * Proxy vs CORS
   * Run frontend
5. Serve React from Spring Boot (production)

   * Build steps
   * Maven/Gradle integration options
6. Tips, troubleshooting & common errors
7. Folder structure (suggested)
8. Next steps & enhancements

---

## 1. Overview

We'll implement a minimal "Notes" example: a **Note**
 entity with **id**
 and **text**
. Spring Boot exposes CRUD endpoints (**/api/notes**
) and React displays notes and lets users add new notes.

This tutorial keeps persistence optional (H2 or in-memory) — the focus is on connecting the two sides.

---

## 2. Prerequisites

* Java 11+ (or 17+ recommended)
* Maven or Gradle
* Node.js 16+ (or current LTS) and npm/yarn
* Basic knowledge of React and Spring Boot
* Optional: Postman or curl for API testing

---

## 3. Backend (Spring Boot)

### 3.1 Create project

Use [https://start.spring.io](https://start.spring.io) or your IDE to scaffold a Spring Boot project. Choose Java, Maven (or Gradle), and add the **Spring Web** dependency. Optionally add **Spring Data JPA** and **H2 Database** if you want persistence.

Example **pom.xml**
 (only the relevant dependencies shown):

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>

  <!-- Optional: persistence for demo -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
  </dependency>
  <dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
  </dependency>
</dependencies>
```

### 3.2 Simple model, repository and controller

**Model (Note.java)**

```java
package com.example.demo.model;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Note {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String text;

    // constructors, getters, setters

    public Note() {}

    public Note(String text) {
        this.text = text;
    }

    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getText() { return text; }
    public void setText(String text) { this.text = text; }
}
```

**Repository (NoteRepository.java)**

```java
package com.example.demo.repository;

import com.example.demo.model.Note;
import org.springframework.data.jpa.repository.JpaRepository;

public interface NoteRepository extends JpaRepository<Note, Long> { }
```

**Controller (NoteController.java)**

```java
package com.example.demo.controller;

import com.example.demo.model.Note;
import com.example.demo.repository.NoteRepository;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/notes")
public class NoteController {

    private final NoteRepository repo;

    public NoteController(NoteRepository repo) { this.repo = repo; }

    @GetMapping
    public List<Note> list() {
        return repo.findAll();
    }

    @PostMapping
    public Note create(@RequestBody Note note) {
        return repo.save(note);
    }

    @DeleteMapping("/{id}")
    public void delete(@PathVariable Long id) { repo.deleteById(id); }
}
```

### 3.3 CORS

If you serve the frontend with a dev server (e.g. **npm start**
 on **localhost:3000**
) and backend runs on another port (e.g. **8080**
), you must allow CORS. Options:

1. Configure CORS on specific controller(s):

```java
@CrossOrigin(origins = "http://localhost:3000")
@RestController
@RequestMapping("/api/notes")
public class NoteController { ... }
```

2. Global CORS config (recommended for development):

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig {
    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/api/**").allowedOrigins("http://localhost:3000");
            }
        };
    }
}
```

**Note on production:** handle CORS carefully and only allow trusted origins.

### 3.4 application.properties (H2 example)

```
spring.datasource.url=jdbc:h2:mem:demo;DB_CLOSE_DELAY=-1
spring.datasource.driverClassName=org.h2.Driver
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.h2.console.enabled=true
spring.jpa.hibernate.ddl-auto=update
```

### 3.5 Run backend

From the project root:

```bash
mvn spring-boot:run
# or
./mvnw spring-boot:run
```

Open **http://localhost:8080/api/notes**
 (GET) with Postman or curl to confirm.

---

## 4. Frontend (React)

### 4.1 Create project (Vite recommended)

```bash
npm create vite@latest react-notes -- --template react
cd react-notes
npm install
npm install axios
```

(Alternatively **create-react-app**
 is fine.)

### 4.2 Axios helper (**src/api.js**
)

Create a simple API helper to centralize requests.

```javascript
import axios from 'axios';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL || '/api',
});

export default api;
```

In development you can set **VITE_API_BASE_URL=http://localhost:8080/api**
 in **.env**
 or use a proxy (explained below).

### 4.3 Simple React component (**src/App.jsx**
)

```jsx
import React, { useEffect, useState } from 'react';
import api from './api';

export default function App() {
  const [notes, setNotes] = useState([]);
  const [text, setText] = useState('');

  useEffect(() => {
    fetchNotes();
  }, []);

  async function fetchNotes() {
    const res = await api.get('/notes');
    setNotes(res.data);
  }

  async function addNote(e) {
    e.preventDefault();
    if (!text.trim()) return;
    await api.post('/notes', { text });
    setText('');
    fetchNotes();
  }

  async function removeNote(id) {
    await api.delete(**/notes/${id}**
);
    fetchNotes();
  }

  return (
    <div style={{ padding: 24 }}>
      <h1>Notes</h1>
      <form onSubmit={addNote}>
        <input value={text} onChange={e => setText(e.target.value)} placeholder="Write note" />
        <button type="submit">Add</button>
      </form>
      <ul>
        {notes.map(n => (
          <li key={n.id}>
            {n.text} <button onClick={() => removeNote(n.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### 4.4 Proxy (dev) vs CORS

**Proxy (React dev server)**

If you don't want to configure CORS on the backend during development, use the proxy option in **package.json**
 (works with CRA). For Vite, use **vite.config.js**
 proxy.

Example **vite.config.js**
 proxy snippet:

```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      '/api': 'http://localhost:8080'
    }
  }
})
```

This forwards **/api/***
 calls to the Spring Boot server.

**CORS approach**

If you allow CORS in Spring Boot (see section 3.3), you can call the backend directly from React using **http://localhost:8080/api**
 as the base URL.

### 4.5 Run frontend

```bash
npm run dev
# or
npm start
```

Visit the dev server (e.g. **http://localhost:5173**
 for Vite or **http://localhost:3000**
 for CRA).

---

## 5. Serve React from Spring Boot (production)

To deploy a single artifact, build the React app and copy the static files into Spring Boot's **resources/static**
 (or **resources/public**
) directory. Spring Boot will automatically serve those files.

### 5.1 Build React

```bash
npm run build
# build output typically in **dist/**
 (Vite) or **build/**
 (CRA)
```

### 5.2 Copy files into Spring Boot

Copy the contents of the React **dist/**
 (or **build/**
) to **src/main/resources/static/**
 in your Spring Boot project. When the jar runs, visiting **/**
 serves the React app, and **fetch('/api/...')**
 hits your REST endpoints.

### 5.3 Automate with Maven (optional)

Use the **frontend-maven-plugin**
 or **maven-resources-plugin**
 to run **npm install**
 and **npm run build**
 during the Maven build, and then copy the build output into **src/main/resources/static**
.

A minimal **frontend-maven-plugin**
 usage (pom snippet):

```xml
<plugin>
  <groupId>com.github.eirslett</groupId>
  <artifactId>frontend-maven-plugin</artifactId>
  <version>1.12.1</version>
  <executions>
    <execution>
      <id>install node and npm</id>
      <goals><goal>install-node-and-npm</goal></goals>
      <configuration><nodeVersion>v16.20.0</nodeVersion></configuration>
    </execution>
    <execution>
      <id>npm install</id>
      <goals><goal>npm</goal></goals>
      <configuration><arguments>install</arguments></configuration>
    </execution>
    <execution>
      <id>npm build</id>
      <goals><goal>npm</goal></goals>
      <configuration><arguments>run build</arguments></configuration>
    </execution>
  </executions>
</plugin>
```

**Note:** plugin versions and node versions change over time — adjust as needed.

---

## 6. Tips, troubleshooting & common errors

* **CORS errors**: Check browser console; either set up a proxy in dev server or enable CORS on backend.
* **404 when loading static assets**: Ensure the React build files are copied to **src/main/resources/static**
 and that **index.html**
 is present.
* **API 500 errors**: Check Spring Boot logs for stack traces.
* **Mismatched base URLs**: Use environment variables (**.env**
, **VITE_**
 prefix for Vite) to set **VITE_API_BASE_URL**
.
* **React router 404s in production**: If you use client-side routing (react-router), configure Spring Boot to forward unknown paths to **index.html**
.

Example controller to forward all paths to index (for SPA):

```java
@Controller
public class SpaController {
  @RequestMapping(value = "/{[path:[^\\.]*}")
  public String redirect() {
    return "forward:/index.html";
  }
}
```

(Adjust mapping according to needs.)

---

## 7. Suggested folder structure

```
project-root/
├─ backend/ (Spring Boot)
│  ├─ src/main/java/...
│  ├─ src/main/resources/application.properties
│  └─ src/main/resources/static/  <-- production React files
└─ frontend/ (React - Vite or CRA)
   ├─ src/
   ├─ package.json
   └─ vite.config.js
```

Or keep them as separate repositories if you prefer independent deployment.

---

## 8. Next steps & enhancements

* Add validation and DTOs on backend.
* Add authentication (JWT or OAuth2) and secure the API.
* Use React Query or SWR for caching and background fetch.
* Add tests: unit tests for Spring services and Jest/RTL for React components.
* CI/CD: build frontend, run tests, build backend, and deploy a single artifact or containerize each service in Docker.

---

## 9. Quick reference commands

```bash
# Backend
mvn spring-boot:run
# Frontend (dev)
npm run dev
# Frontend (build)
npm run build
# Put build output into backend's src/main/resources/static and run mvn package
mvn package
```

---

## 10. Further reading

* [Spring Boot reference docs](https://docs.spring.io/spring-boot/index.html)
* [React docs and Vite docs](https://vite.dev/guide/)
* [Axios documentation](https://axios-http.com/docs/intro)