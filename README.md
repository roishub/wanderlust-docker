# Wanderlust - Your Ultimate Travel Blog 🌍✈️

WanderLust is a simple MERN travel blog website ✈ This is an open source project that i have taken from the following link- https://github.com/krishnaacharyaa/wanderlust , to take up a project to dockerize this application.


To start with the 
## Steps that i have taken to Dockerize this Application

### Setting up the Backend

1. **create backend Docker file**

   - This Dockerfile sets up a Node.js environment(using the slim variant) in a Docker container, then copies application files, installs dependencies, sets environment variables, exposes port 5000, and starts the application with 'npm start'.

2. **create frontend Docker file**

   - Same base image as of that the backend is used here. It copies application files into the /app directory, installs dependencies with npm i, copies the .env.sample file as .env.local, exposes port 5173, and starts the application in development mode with npm run dev -- --host
   

3. **creating docker-compose.yaml**
   
   - It is defined in the root folder of the project directory. This Docker Compose file defines three services: mongodb, backend, and frontend.
   - MongoDB Service (mongodb):
   Container Name: mongo
   Image: mongo:latest
   Volumes: Binds the local directory ./backend/data to /data inside the container i.e., transfers data from './backend/data' to the './data' folder inside mongodb.
   Ports: Forwards port 27017 from the host to port 27017 in the container.

   Backend Service (backend):
   Container Name: backend
   Build: Builds the Docker image for the backend service using the Dockerfile located in ./backend.
   Env File: Uses environment variables defined in ./backend/.env.sample.
   Ports: Forwards port 5000 from the host to port 5000 in the container.
   Depends On: Specifies that this service depends on the mongodb service, ensuring that the mongodb service is started before the backend service.

   Frontend Service (frontend):
   Container Name: frontend
   Build: Builds the Docker image for the frontend service using the Dockerfile located in ./frontend.
   Env File: Uses environment variables defined in ./frontend/.env.sample.
   Ports: Forwards port 5173 from the host to port 5173 in the container.


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

   This will enable the web app to perform as it should.
6. **Configure Environment Variables**

   ```bash
   cp .env.sample .env
   ```


   ```

## 🌟 

## 💖 Show Your Support

If you find this project interesting and inspiring, please consider showing your support by starring it on GitHub! Your star goes a long way in helping me reach more developers and encourages me to keep enhancing the project.

🚀 Feel free to get in touch with me for any further queries or support, happy to help :)
