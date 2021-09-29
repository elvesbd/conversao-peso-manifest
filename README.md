# conversao-peso

FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build

WORKDIR /src

COPY ["ConversaoPeso/ConversaoPeso.csproj", "ConversaoPeso/"]

RUN dotnet restore "ConversaoPeso/ConversaoPeso.csproj"

COPY . .

WORKDIR "/src/ConversaoPeso"

RUN dotnet build "ConversaoPeso.csproj" -c Release -o /app/build

FROM build AS publish

RUN dotnet publish "ConversaoPeso.csproj" -c Release -o /app/publish

FROM base AS final

WORKDIR /app

COPY --from=publish /app/publish .

ENTRYPOINT ["dotnet", "ConversaoPeso.dll"]

FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /app
COPY ConversaoPeso.Web/ConversaoPeso.Web.csproj ./ConversaoPeso.Web/
RUN dotnet restore ConversaoPeso.Web/ConversaoPeso.Web.csproj
COPY . .
#COPY ConversaoPeso.Web/. ./ConversaoPeso.Web/
WORKDIR /app/ConversaoPeso.Web
RUN dotnet build ConversaoPeso.Web.csproj -c Release -o /app/build
FROM build AS publish
RUN dotnet publish ConversaoPeso.Web.csproj -c Release -o /app/publish
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "ConversaoPeso.Web.dll"]
