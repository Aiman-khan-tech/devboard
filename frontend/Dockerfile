# Base image (For npm Node.js)
FROM node:24

# Folder to store the code
WORKDIR /app

# Copy the code from the host machine to the container
COPY . .

# Install the required dependencies
RUN npm install --legacy-peer-deps

# Port for container
EXPOSE 5173

# Run the application
CMD ["npm","run","dev","--","--host","0.0.0.0","--port","5173"]



