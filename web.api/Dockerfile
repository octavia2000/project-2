# Use the official .NET Core SDK image as a build environment
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build-env

# Set working directory for the build env
RUN mkdir ${APP_HOME}
WORKDIR ${APP_HOME}

# Copy project configuration and restore any dependencies (NuGet packages)
# A ".csproj" file contains metadata and configuration settings for your C# project
COPY ["*.csproj", "${APP_HOME}/web.api/"]
RUN cd ${APP_HOME}/web.api; dotnet restore

# Copy the remaining source code and build the application
COPY [".", "${APP_HOME}/web.api"]
RUN cd ${APP_HOME}/web.api; dotnet build -c Release -o build

# Execute UnitTest 
# The --verbosity:normal option specifies the level of detail in the output of the command. 
# It is set to normal, which means that the output will include information about each test that is run, including its status and duration.
FROM build-env AS test-env
WORKDIR $APP_HOME/web.api.Test
RUN dotnet test --verbosity:normal 

# Publish a release
# The "-c" flag is used in the "dotnet publish" command to specify the build configuration to use
# The build configuration determines the build settings for the project, such as whether to include debugging symbols or optimize the output

# The "-o" flag is used in the dotnet publish command to specify the output directory for the published application
# By default, the output directory is ./bin/<BUILD-CONFIGURATION>/<FRAMEWORK>/publish/
# In this case, -o out specifies that the output directory should be out
# This means that the compiled application will be published to the out folder in the working directory

FROM build-env AS publish-env
WORKDIR ${APP_HOME}/web.api

# Build runtime image as final environment
FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS final-env

# Set the working directory inside the final environment
WORKDIR /app

# The text "ENV ASPNETCORE_URLS=http://+:5131" sets an environment variable named "ASPNETCORE_URLS" to the value "http://+:5131"
# This environment variable is used by the .NET Core runtime to specify the URLs that the application should listen on
# In this case, the application will listen on all network interfaces ("+") on port "5131"
ENV ASPNETCORE_URLS=http://+:5131

# The text "ENV ASPNETCORE_ENVIRONMENT=docker" sets another environment variable named "ASPNETCORE_ENVIRONMENT" to the value "docker"
# This environment variable is used by the .NET Core runtime to specify the environment in which the application is running
# In this case, it is set to "docker", indicating that the application is running inside a Docker container
ENV ASPNETCORE_ENVIRONMENT=docker

# Copy the published application from the build environment to the final environment

# Define the command to start the application (dll === Dynamic Link Libraries)
ENTRYPOINT ["dotnet", "web.api.dll"]
