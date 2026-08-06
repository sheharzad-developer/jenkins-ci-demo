# Use an official Node.js runtime
FROM node:22-alpine

# Set the working directory inside the container
WORKDIR /app

# Copy package files first (helps Docker cache dependencies)
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application
COPY . .

# Expose the application port
EXPOSE 3000

# Start the application
CMD ["npm", "start"]