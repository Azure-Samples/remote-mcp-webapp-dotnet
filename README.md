# Model Context Protocol (MCP) Server - .NET Implementation

This project contains a .NET web app implementation of a Model Context Protocol (MCP) server. The application is designed to be deployed to Azure App Service.

The MCP server provides an API that follows the Model Context Protocol specification, allowing AI models to request additional context during inference.

## Key Features

- Complete implementation of the MCP protocol in C#/.NET using [MCP csharp-sdk](https://github.com/modelcontextprotocol/csharp-sdk)
- Azure App Service integration
- Custom tools support

## Project Structure

- `src/` - Contains the main C# project files
  - `Program.cs` - The entry point for the MCP server
  - `Tools/` - Contains custom tools that can be used by models via the MCP protocol
    - `MultiplicationTool.cs` - Example tool that performs multiplication operations
    - `TemperatureConverterTool.cs` - Tool for converting between Celsius and Fahrenheit
    - `WeatherTools.cs` - Tools for retrieving weather forecasts and alerts
- `infra/` - Contains Azure infrastructure as code using Bicep
  - `main.bicep` - Main infrastructure definition
  - `resources.bicep` - Resource definitions
  - `main.parameters.json` - Parameters for deployment

## Prerequisites

- [Azure Developer CLI](https://aka.ms/azd)
- [.NET 9 SDK](https://dotnet.microsoft.com/download)
- For local development with VS Code:
  - [Visual Studio Code](https://code.visualstudio.com/)
- MCP C# SDK:
  ```bash
  dotnet add package ModelContextProtocol --prerelease
  ```

## Local Development

### Run the Server Locally

1. Clone this repository
2. Navigate to the project directory
   ```bash
   cd src
   ```
3. Install required packages
   ```bash
   dotnet restore
   ```
4. Run the project:
   ```bash
   dotnet run
   ```
4. The MCP server will be available at `http://localhost:5000`
5. When you're done, press Ctrl+C in the terminal to stop the app

### Testing the Available Tools

The server provides these tools:
- **Multiplication**: `Multiply` - Multiplies two numbers
- **Temperature Conversion**: 
  - `CelsiusToFahrenheit` - Converts temperature from Celsius to Fahrenheit
  - `FahrenheitToCelsius` - Converts temperature from Fahrenheit to Celsius
- **Weather Data**:
  - `GetAlerts` - Get active weather alerts for a US state (provide state code like "CA", "TX")
  - `GetForecast` - Get weather forecast for coordinates (provide latitude and longitude)

### Connect to the Local MCP Server

#### Using VS Code - Copilot Agent Mode

1. **Add MCP Server** from command palette and add the URL to your running server's HTTP endpoint:
   ```
   http://localhost:5000
   ```
2. **List MCP Servers** from command palette and start the server
3. In Copilot chat agent mode, enter a prompt to trigger the tool:
   ```
   Multiply 3423 and 5465
   ```
4. When prompted to run the tool, consent by clicking **Continue**

You can ask things like:
- What's the weather forecast for San Francisco? (latitude: 37.7749, longitude: -122.4194)
- Are there any weather alerts in California?
- Convert 25 degrees Celsius to Fahrenheit

#### Using MCP Inspector

1. In a **new terminal window**, install and run MCP Inspector:
   ```bash
   npx @modelcontextprotocol/inspector
   ```
2. CTRL+click the URL displayed by the app (e.g. http://localhost:5173/#resources)
3. Set the transport type to `HTTP`
4. Set the URL to your running server's HTTP endpoint and **Connect**:
   ```
   http://localhost:5000
   ```
5. **List Tools**, click on a tool, and **Run Tool**

## Deploy to Azure

1. Login to Azure:
   ```bash
   azd auth login
   ```

2. Initialize your environment:
   ```bash
   azd env new
   ```

3. Deploy the application:
   ```bash
   azd up
   ```

   This will:
   - Build the .NET application
   - Provision Azure resources defined in the Bicep templates
   - Deploy the application to Azure App Service

### Connect to Remote MCP Server

#### Using MCP Inspector
Use the web app's URL:
```
https://<webappname>.azurewebsites.net
```

#### Using VS Code - GitHub Copilot
Follow the same process as with the local app, but use your App Service URL:
```
https://<webappname>.azurewebsites.net
```

## Clean up resources

When you're done working with your app and related resources, you can use this command to delete the function app and its related resources from Azure and avoid incurring any further costs:

```shell
azd down
```

## Custom Tools

The project includes several sample tools in the `Tools` directory:
- `MultiplicationTool.cs` - Performs multiplication operations
- `TemperatureConverterTool.cs` - Converts between Celsius and Fahrenheit
- `WeatherTools.cs` - Retrieves weather forecasts and alerts

To add new tools:
1. Create a new class in the `Tools` directory
2. Implement the MCP tool interface
3. Register the tool in `Program.cs`
