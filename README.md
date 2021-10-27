# SDG Ontology Visualizer

A tool for visualizing ontologies related to UN's sustainable development goals

[![Netlify Status](https://api.netlify.com/api/v1/badges/ae7d5c8b-7978-4f95-9b6b-fd1b40d40616/deploy-status)](https://app.netlify.com/sites/epic-ardinghelli-d1ee4d/deploys)

[![CI](https://github.com/ntnu-informatikk-2021/SDG-ontology-visualizer/actions/workflows/main.yml/badge.svg)](https://github.com/ntnu-informatikk-2021/SDG-ontology-visualizer/actions/workflows/main.yml)

[Deployed project](https://epic-ardinghelli-d1ee4d.netlify.app/)

# Deploying

Prerequisites:

- Docker supporting compose files >= 3.9

- A valid GraphDB license file

**IMPORTANT**: To ease deployment, all containers are pre-built and uploaded to Docker Hub. This also includes environment variables like **access secrets, JWT-tokens and configuration-urls**. If you want to change any of these settings, change the environment-settings in `frontend/` or `backend/`, rebuild the images with your own tags and use these tags in the compose-files in `deployment/`.

### Database, API, backend, or the whole application

If you only want to run the API or frontend by itself, you can skip this step. If you do so, remember to set the correct environment variables inside their respective compose files.

1. Navigate to `deployment/`

2. Add your valid GraphDB license file to the `license` directory, naming it `graphdb.license`

3. Copy `.env.example` and rename it `.env`. Change the values if needed.

4. In `build_frontend.Dockerfile`, set `REACT_APP_BACKEND_URL` to the publicly reachable address of the API.

5. Run either...

   1. **Frontend only:** `docker compose -f docker-compose-frontend.yml up`
   2. **Database only:** `docker compose -f docker-compose-db.yml up`
   3. **Whole backend:** `docker compose -f docker-compose-backend.yml up`
   4. **Whole application:** `docker compose up`

6. When the cluster is running and ready, go to http://localhost:7200 (or the externally reachable URL). On the left side of the screen, go to "Setup" and then "Users and Access". Click "Create new user" and make a user with credentials matching the `GRAPHDB_USERNAME` and `GRAPHDB_PASSWORD`variables set in `.env`. Make sure the user has Read-access to the TK_SDG-repository.

7. **OPTIONAL:** If you want to add fictional data for the GDC-visualization, run `utils/datagen.py` after setting up the backend (see `utils/README.md` for more details).

8. If you're running the backend or whole application, restart the cluster.

Default ports:

- **DB:** 7200
- **API:** 3001
- **Frontend:** 80

### Only API or frontend

1. See point **3** and **4** above.

2. Run `docker compose -f docker-compose-api.yml up` or `docker compose -f docker-compose-frontend.yml up`

# Develop

## How to setup

1. Clone the repo
2. Run `yarn` in **both** backend and frontend folders
3. Create a file named _.env_ in the backend folder and paste your credentials as well as the IP of the server running GraphDB with port 7200. The structure of your _.env_ file can be copied from _.env.example_.
4. Run `yarn start` both in frontend and backend folders to start both the web application as well as the Express server.

## Setting up the dockerized development environment

Prerequisites:

- Docker

- NodeJS

- Valid GraphDB license-file

### Backend

1. Make sure your Docker-daemon is running.

2. Place your `graphdb.license` file in `backend/database/conf`

3. Copy the `backend/.env.example`-file, and rename the copy `.env`

4. In `backend/`, run `docker-compose -f docker-compose-backend.yml up`

5. When the Docker-cluster is running, go to http://localhost:7200. On the left side of the screen, go to "Setup" and then "Users and Access". Click "Create new user" and make a user with credentials matching the `GRAPHDB_USERNAME` and `GRAPHDB_PASSWORD` fields in `backend/.env`. Make sure the user has Read-access to the TK_SDG-repository.

6. Stop the docker-compose cluster, and repeat step 4.

7. The backend should now be running, and accessible at http://localhost:3001

If you only want to run the GraphDB-database, replace `docker-compose-backend.yml` with `docker-compose-db.yml` in step 2.

### Frontend

1. Make sure the API is available on http://localhost:3001 (follow the steps above)

2. Copy the `frontend/.env.example`-file, and rename the copy `.env`

3. In `frontend/`, run `docker-compose up --build`

4. The app should now be accesible at https://localhost

### Frontend (development)

You can also develop using a devserver on a Docker container. It uses Docker-volumes to ensure that the container's workspace matches the codebase on your computer. This means that hot-reloading will work the same way as if you ran the devserver locally.

To use the development container, follow these steps:

1. Make sure the API is available on http://localhost:3001 (follow the steps above)

2. Copy the `frontend/.env.example`-file, and rename the copy `.env` (if you haven't done so already).

3. In `frontend/`, run `docker-compose -f docker-compose-dev.yml up --build`

4. The app should now be accesible at http://localhost:3000

### If changes are made to the ontology

Sometimes, changes to the `/backend/database/ontology/SDG_Ontology.owl` file are made. To implement these into your database, you have to do the following:

1. Before composing the backend Docker containers in Backend step 4, run `docker-compose -f docker-compose-backend.yml build --no-cache`.

This forces the containers to rebuild with the new ontology.

2. Continue with step 5 to 7 in the backend setup guide.
