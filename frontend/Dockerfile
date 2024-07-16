# Stage 1: Build the React application (frontend)
FROM node:lts-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build  # Or the command to build your React app

# Copy the rest of the frontend application code

# Stage 2: Create the final image (backend can be integrated here)
FROM nginx:alpine

COPY --from=builder /app/build /usr/share/nginx/html



EXPOSE 80
