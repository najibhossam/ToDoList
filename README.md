# ToDoList

> **Application de gestion de listes de tâches**

---

## 📋 Présentation

ToDoList est une application full‑stack permettant de créer, lire, mettre à jour et supprimer des listes de tâches. Le backend est développé avec **FastAPI** et MongoDB Atlas, l'API est servie par **Uvicorn**, et le frontend est une SPA **React** (create-react-app) communiquant via **axios**.

**Fonctionnalités** :
- Liste des listes de tâches (`GET /api/lists`)
- Création de liste (`POST /api/lists`)
- Récupération d'une liste (`GET /api/lists/{id}`)
- Ajout / suppression de tâches dans une liste
- Statut et dates de création

---

## 🔧 Architecture

```mermaid
graph TD
  subgraph Client
    A[React SPA] -->|axios REST| B[Apache Proxy]
  end
  subgraph Serveur
    B -->|proxy /fastapi/api/…| C[Uvicorn FastAPI]
    C --> D[MongoDB Atlas]
  end
```

---

## ⚙️ Prérequis

- **Python 3.11** et **venv**
- **Node 18** et **npm 10**
- **MongoDB Atlas** (cluster opérationnel)

---

## 🚀 Installation locale

### Backend

```bash
# Clone
git clone https://github.com/najibhossam/ToDoList.git
cd ToDoList/backend

# Environnement Python
python3.11 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt

# Variables d’environnement (exemple)
export MONGODB_URI="mongodb+srv://<user>:<pwd>@cluster0.wegtn.mongodb.net/todo?retryWrites=true&w=majority"
export DEBUG=1

# Lancer l’API
uvicorn server:app --reload --host 127.0.0.1 --port 8000
```

Ouvrez `http://localhost:8000/docs` pour Swagger UI.

### Frontend

```bash
cd ToDoList/frontend
npm install
export REACT_APP_API_BASE_URL="http://localhost:8000/api"
npm start
```

Ouvrez `http://localhost:3000` pour l’interface.

---

## 🔑 Variables d’environnement

| Nom            | Exemple                                                         | Description                          |
| -------------- | --------------------------------------------------------------- | ------------------------------------ |
| `MONGODB_URI`  | `mongodb+srv://user:pwd@cluster0.wegtn.mongodb.net/todo?…`      | URI de connexion MongoDB Atlas       |
| `DEBUG`        | `0` ou `1`                                                      | Mode debug (logs détaillés)          |
| `REACT_APP_API_BASE_URL` | `/fastapi/api` ou `http://localhost:8000/api`          | BaseURL pour axios côté React        |

---

## 🖥️ API REST

```text
GET    /api/lists
POST   /api/lists            # { name: string }
GET    /api/lists/{id}
PUT    /api/lists/{id}/items # { description: string }
DELETE /api/lists/{id}/items/{item_id}
```

Toutes les routes renvoient du JSON, CORS activé par défaut.

---

## 📦 Déploiement en production

1. **Backend : systemd + Uvicorn**
   ```bash
   # Créer venv, installer requirements
   cd /opt/fastapi/ToDoList/backend
   python3.11 -m venv venv
   source venv/bin/activate && pip install -r requirements.txt
   deactivate

   # Fichier /etc/systemd/system/fastapi.service
   [Unit]
   Description=FastAPI (ToDoList)
   After=network.target

   [Service]
   User=fastapi
   Group=fastapi
   WorkingDirectory=/opt/fastapi/ToDoList/backend/src
   EnvironmentFile=/etc/fastapi/fastapi.env
   ExecStart=/opt/fastapi/venv/bin/uvicorn server:app --host 127.0.0.1 --port 8000 --proxy-headers
   Restart=on-failure

   [Install]
   WantedBy=multi-user.target
   ```

2. **Frontend : React Build**
   ```bash
   cd /opt/fastapi/ToDoList/frontend
   npm ci && npm run build
   rsync -a build/ /home/${CPUSER}/public_html/fastapi/
   ```

---

*Bon développement !*
