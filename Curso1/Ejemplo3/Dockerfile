FROM mcr.microsoft.com/dotnet/core/aspnet:3.1
ENV ASPNETCORE_URLS=http://+:81
EXPOSE 81
WORKDIR /app
COPY publish/. .
ENTRYPOINT ["dotnet", "Ejemplo3.dll"]
