# Stage 1: Build Image
# Sử dụng SDK image để build và restore dependencies
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /src

# Copy project files and restore dependencies (tối ưu hóa Docker layer caching)
# Note: Dấu .. nghĩa là trỏ ra ngoài thư mục Gateway để tìm ShortenUrl và UserService
COPY ["Gateway/Gateway.csproj", "Gateway/"]
COPY ["ShortenUrl/ShortenUrl.csproj", "ShortenUrl/"]
# Giả định Service 1/3 có tên là UserService
COPY ["UserService/UserService.csproj", "UserService/"] 

# Restore dependencies (Chạy từ thư mục gốc của Gateway)
WORKDIR /src/Gateway
RUN dotnet restore "Gateway.csproj"

# Copy source code còn lại
WORKDIR /src
COPY . .

# Publish Gateway (Build ra file chạy)
WORKDIR /src/Gateway
RUN dotnet publish "Gateway.csproj" -c Release -o /app/publish --no-restore

# Stage 2: Final Image (Chỉ chứa runtime)
FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS final
WORKDIR /app
COPY --from=build /app/publish .

# Cấu hình Ocelot để đọc file cấu hình bên ngoài
COPY Gateway/ocelot.json . 

# Cổng mặc định cho ASP.NET Core
EXPOSE 80

# Chạy ứng dụng
ENTRYPOINT ["dotnet", "Gateway.dll"]