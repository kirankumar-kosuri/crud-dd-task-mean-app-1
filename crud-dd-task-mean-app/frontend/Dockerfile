# ================================
# Stage 1: Build Stage
# ================================
FROM node:18-alpine AS builder

WORKDIR /app

# ENV NODE_OPTIONS="--max_old_space_size=1024"

# Copy package files first for better layer caching
COPY package.json package-lock.json ./

# Install dependencies
RUN npm ci

# Copy the rest of the source code
COPY . .

# Build the Angular app for production
RUN npm run build -- --configuration production

# ================================
# Stage 2: Production Stage
# ================================
FROM nginx:1.25-alpine AS production

# Create a non-root user and group
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Copy built Angular dist files from builder stage
# Angular 15 outputs to dist/<project-name> — adjust folder name if needed
COPY --from=builder /app/dist/ /usr/share/nginx/html/

# Copy custom nginx config
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Set ownership to non-root user
RUN chown -R appuser:appgroup /usr/share/nginx/html \
    && chown -R appuser:appgroup /var/cache/nginx \
    && chown -R appuser:appgroup /var/log/nginx \
    && touch /var/run/nginx.pid \
    && chown appuser:appgroup /var/run/nginx.pid

USER appuser

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]