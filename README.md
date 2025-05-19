# Healthcare-Appointment-Management-System
# Healthcare Appointment Management System 🏥

A full-stack .NET Core application that enables patients to book appointments, doctors to manage schedules, and admins to oversee the entire ecosystem. Built with Microservices, Angular/Vue, SQL Server, and deployed on Azure.

## 🛠 Tech Stack

- **Frontend**: VueJS with Vuex / Angular 14 + Material
- **Backend**: ASP.NET Core Web API + Entity Framework Core
- **Database**: SQL Server, Azure SQL
- **Microservices**: Appointment Service, Notification Service, User Service
- **Cloud**: Azure App Service, Azure Functions, Azure SQL
- **Messaging**: RabbitMQ or Azure Service Bus
- **Authentication**: JWT Tokens with role-based access control
- **CI/CD**: Azure DevOps Pipelines with Docker

## 📐 Architecture

![Architecture Diagram](link_to_diagram_or_excalidraw)

## 🔧 How to Run Locally

### Backend
```bash
cd Appointment.API
dotnet restore
dotnet run


cd appointment-ui
npm install
npm run serve

dotnet ef database update

docker-compose up --build

Appointment.API/
  Controllers/
  Models/
  Services/
  Repositories/
  appsettings.json

Appointment.NotificationService/
Appointment.UserService/
appointment-ui/  # Vue/Angular frontend
docker-compose.yml
azure-pipelines.yml
README.md



---

## 🐳 `docker-compose.yml`

```yaml
version: '3.4'

services:
  appointment.api:
    build: ./Appointment.API
    ports:
      - "5000:80"
    environment:
      - ConnectionStrings__DefaultConnection=Server=sqlserver;Database=AppointmentsDB;User Id=sa;Password=Your_password123;
    depends_on:
      - sqlserver

  sqlserver:
    image: mcr.microsoft.com/mssql/server:2019-latest
    environment:
      SA_PASSWORD: "Your_password123"
      ACCEPT_EULA: "Y"
    ports:
      - "1433:1433"

  frontend:
    build: ./appointment-ui
    ports:
      - "8080:8080"


trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

variables:
  buildConfiguration: 'Release'

steps:
- task: UseDotNet@2
  inputs:
    packageType: 'sdk'
    version: '7.0.x'

- task: DotNetCoreCLI@2
  inputs:
    command: 'restore'
    projects: '**/*.csproj'

- task: DotNetCoreCLI@2
  inputs:
    command: 'build'
    arguments: '--configuration $(buildConfiguration)'

- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    arguments: '--configuration $(buildConfiguration) --output $(Build.ArtifactStagingDirectory)'

- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(Build.ArtifactStagingDirectory)'
    ArtifactName: 'drop'

- task: AzureWebApp@1
  inputs:
    azureSubscription: '<Your Azure Subscription>'
    appName: '<Azure App Service Name>'
    package: '$(Build.ArtifactStagingDirectory)/**/*.zip'
