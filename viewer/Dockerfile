FROM mcr.microsoft.com/dotnet/aspnet:8.0.2-jammy AS base
WORKDIR /App
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:8.0.201-jammy AS build-env
WORKDIR /App

# Copy everything
COPY ["global.json", "."]
COPY ["Tools/azure-event-grid-viewer/viewer/viewer.csproj", "Tools/azure-event-grid-viewer/viewer/"]

# Restore as distinct layers
RUN dotnet restore "Tools/azure-event-grid-viewer/viewer/viewer.csproj"
COPY . .
WORKDIR "/App/Tools/azure-event-grid-viewer/viewer/"
RUN dotnet build "./viewer.csproj" -c Release -o /App/Build

# Build and publish a release
FROM build-env AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./viewer.csproj" -c Release -o /App/publish /p:UseAppHost=false

# Build runtime image
FROM base AS final
WORKDIR /App
COPY --from=publish /App/publish .
ENTRYPOINT ["dotnet", "viewer.dll"]