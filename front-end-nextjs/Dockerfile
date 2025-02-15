# syntax=docker/dockerfile:1

# Define the Node.js version as a build argument
ARG NODE_VERSION=18

################################################################################
# Base Stage: Use Node.js Alpine as the base image
################################################################################
FROM node:${NODE_VERSION}-alpine AS base

# Set the working directory
WORKDIR /usr/src/app

################################################################################
# Deps Stage: Install production dependencies
################################################################################
FROM base AS deps

# Install dependencies using cache and bind mounts for efficiency
RUN --mount=type=bind,source=package.json,target=package.json \
    --mount=type=bind,source=package-lock.json,target=package-lock.json \
    --mount=type=cache,target=/root/.npm \
    npm ci --omit=dev

################################################################################
# Build Stage: Install dev dependencies and build the application
################################################################################
FROM deps AS build

# Install development dependencies if needed
RUN --mount=type=bind,source=package.json,target=package.json \
    --mount=type=bind,source=package-lock.json,target=package-lock.json \
    --mount=type=cache,target=/root/.npm \
    npm ci

# Copy source files and build the application
COPY . .
RUN npm run build

################################################################################
# Final Stage: Create a minimal runtime image
################################################################################
FROM base AS final

# Set the environment to production
ENV NODE_ENV=production

# Run the application as a non-root user for security
USER node

# Copy only necessary files for runtime
COPY package.json ./
COPY --from=deps /usr/src/app/node_modules ./node_modules
COPY --from=build /usr/src/app ./

# Expose the application port
EXPOSE 3000

# Start the application
CMD ["npm", "start"]
