# ================================
# Stage 1: Build / Install Stage
# ================================
FROM node:18-alpine AS builder

WORKDIR /app

# Copy package files first for better layer caching
COPY package.json ./

COPY *.js .

# Install only production dependencies
RUN npm install 

# ================================
# Stage 2: Production Stage
# ================================
FROM node:18-alpine

# Create a non-root user and group
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

# Copy installed node_modules from builder stage
COPY --from=builder /app/node_modules ./node_modules

# Copy application source code
COPY --chown=appuser:appgroup . .

# Switch to non-root user
USER appuser

EXPOSE 8081

CMD ["node", "server.js"]