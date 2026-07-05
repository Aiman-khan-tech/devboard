# Base image (For npm Node.js)
FROM dhi.io/node:24-alpine3.24-dev AS builder

# Folder to store the code
WORKDIR /app

COPY package*.json ./

# Install the required dependencies
RUN npm install --legacy-peer-deps

# Copy the code from the host machine to the container
COPY . .

FROM dhi.io/node:24-alpine3.24

WORKDIR /app

ENV PATH=/app/node_modules/ .bin:$PATH

COPY --from=builder --chown=node:node /app /app 

# Port for container
EXPOSE 5173

# Run the application
CMD ["npm","run","dev","--","--host","0.0.0.0","--port","5173"]



