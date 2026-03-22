# builder stage installs dependencies
FROM node:18-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm install --production=false && npm cache clean --force

COPY . .
RUN npm run build --if-present

# runtime stage runs only necessary artifacts
# to ensure smaller final image
FROM node:18-alpine AS runtime

WORKDIR /app
ENV NODE_ENV=production

# Create non-root user for better security
RUN addgroup -g 1001 -S appgroup && \
    adduser -u 1001 -S appuser -G appgroup

COPY --from=builder /app/package*.json ./
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app .
RUN chown -R appuser:appgroup /app
USER appuser

EXPOSE 5000
CMD ["node", "server.js"]
