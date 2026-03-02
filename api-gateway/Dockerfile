# Use the lightweight Alpine version of NGINX
FROM nginx:alpine

# Remove the default NGINX configuration
RUN rm /etc/nginx/conf.d/default.conf

# Copy our custom NGINX configuration file into the container
COPY nginx.conf /etc/nginx/nginx.conf

# Expose port 8080
EXPOSE 8080

# Start NGINX
CMD ["nginx", "-g", "daemon off;"]