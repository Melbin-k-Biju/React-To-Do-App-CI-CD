# Use a specific version of Node.js
FROM node:14-alpine3.12

# Set the working directory inside the container
WORKDIR /app

# Copy only package.json and package-lock.json first (to leverage Docker caching)
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application files
COPY . .

# Expose the port your app runs on
EXPOSE 4000

# Start the application
CMD ["node", "index.js"]
