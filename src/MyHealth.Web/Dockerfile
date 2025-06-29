# Stage 1: Build
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build

# Install Node.js and npm manually
RUN apt-get update && \
    apt-get install -y curl && \
    curl -fsSL https://deb.nodesource.com/setup_16.x | bash - && \
    apt-get install -y nodejs

# Install bower and gulp globally
RUN npm install -g bower gulp

WORKDIR /app

# Copy everything from build context root (the full project)
COPY . .

# Create non-root user and switch
RUN useradd -m builduser
USER builduser

# Go into the correct subfolder inside the image to restore and publish
WORKDIR /app/src/MyHealth.Web

# Restore and publish using the .csproj
RUN dotnet restore
RUN dotnet publish -c Release -o /out

# Stage 2: Runtime
FROM mcr.microsoft.com/dotnet/aspnet:6.0
WORKDIR /app
COPY --from=build /app/src/MyHealth.Web/out .
EXPOSE 80
ENTRYPOINT ["dotnet", "MyHealth.Web.dll"]
