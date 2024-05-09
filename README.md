# Wanderlust - Docker Project

WanderLust is a simple MERN travel blog website ✈ This is an open source project that i have taken from the following link- https://github.com/krishnaacharyaa/wanderlust from #krishnaacharyaa, so that I can try and dockerize this application. I have followed the method given by #LondheShubham153 (Shubham Londhe) in his youtube video which u can access using https://www.youtube.com/watch?v=oUMM1x91McA.

## Steps that I have taken to Dockerize this Application

1. **Create backend Docker file**

   - This Dockerfile sets up a Node.js environment(using the slim variant) in a Docker container, then copies application files, installs dependencies, sets environment variables, exposes port 5000, and starts the application with 'npm start'.

2. **Create frontend Docker file**

   - Same base image as of that the backend is used here. It copies application files into the /app directory, installs dependencies with npm i, copies the .env.sample file as .env.local, exposes port 5173, and starts the application in development mode with npm run dev -- --host
   

3. **creating docker-compose.yaml**
   
   - It is defined in the root folder of the project directory. This Docker Compose file defines three services: mongodb, backend, and frontend.
   - MongoDB Service (mongodb):
   Container Name: mongo
   Image: mongo:latest
   Volumes: Binds the local directory ./backend/data to /data inside the container i.e., transfers data from './backend/data' to the './data' folder inside mongodb.
   Ports: Forwards port 27017 from the host to port 27017 in the container.

   - Backend Service (backend):
   Stage 1 (backend-builder):
   It uses the Node.js 21 image as the base image and sets up a working directory at /app.
   Copies the entire application code into the working directory.
   Runs npm install to install the dependencies specified in your package.json file.
   Stage 2:
   It uses the Node.js 21-slim image as the base image and sets up a working directory at /app.
   Copies the application code from the backend-builder stage into the working directory.
   Copies the .env.sample file to .env in the working directory.
   Exposes port 5000, which is typically used for running Node.js applications.
   Sets the default command to npm start, which is commonly used to start Node.js applications.

   - Frontend Service (frontend):
   Stage 1:
   It uses the Node.js 21 image as the base image.
   Sets up a working directory at /app.
   Copies the entire context (all files and directories) into the working directory.
   Runs npm install --quiet to install dependencies specified in the package.json file.
   This stage likely builds the frontend assets or dependencies required for the application.
   Stage 2:
   It uses the Node.js 21-slim image as the base image.
   Sets up a working directory at /app.
   Copies the built artifacts (such as frontend assets) from the first stage into the working directory.
   Copies the .env.sample file to .env.local in the working directory.
   Exposes port 5173, which is the port on which the frontend application will be accessible.
   Sets the default command to npm run dev -- --host, indicating that the application should be started in development mode with a specific host configuration.



4. **Providing right values inside env files**

   - Open './backend/.env.sample'. Since we are using a mongo in a container. Change the localhost in mongoURI to mongo.
   ```bash
   before:
   MONGODB_URI="mongodb://127.0.0.1/wanderlust"
   after:
   MONGODB_URI="mongodb://mongo/wanderlust"
   ```
   and provide CORS_ORIGIN value under the MONGODB_URI(mention the host IP)
   ```bash
   CORS_ORIGIN="http://<host-IP>:5173"
   ```
   - Now open './frontend/.env.sample'. Give host-IP in place of localhost.
   ```bash
   before:
   VITE_API_PATH="http://localhost:5000"
   after:
   VITE_API_PATH="http://<host-IP>:5000"
   ```

5. **commands to run**

   - Inside the root folder run,
   ```bash
   docker-compose up
   ```
   this will build all the images and start the containers. 
   - Still we haven't yet copied the data, to copy the content from the backend/data/sample_posts.json file and insert it in data folder inside mongoDB container run,
   ```bash
   docker exec -it <mongoDB-container-ID> mongoimport --db wanderlust --collection posts --file ./data/sample_posts.json --jsonArray
   ```

   Refresh your page and then you should be able to access the completed application.
