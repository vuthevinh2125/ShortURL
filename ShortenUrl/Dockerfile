# ====================================================================
# STAGE 1: BUILD - Biên dịch Code và chạy Test
# ====================================================================
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# 1. Copy file cấu hình và Restore Packages (Tối ưu Cache)
# KHÔNG CẦN COPY file .sln nữa
COPY ["ShortenUrl/ShortenUrl.csproj", "ShortenUrl/"] 
COPY ["ShortenUrl.Tests/ShortenUrl.Tests.csproj", "ShortenUrl.Tests/"]

# Khôi phục dependencies: CHUYỂN sang RESTORE bằng Project file
RUN dotnet restore "ShortenUrl/ShortenUrl.csproj" 

# 2. Copy Mã nguồn và Build
COPY . .
WORKDIR /src/ShortenUrl

# Chạy Unit Tests
RUN dotnet test "../ShortenUrl.Tests/ShortenUrl.Tests.csproj" --no-restore 

# Stage Publish: Xuất bản ứng dụng
FROM build AS publish
WORKDIR /src/ShortenUrl
RUN dotnet publish "ShortenUrl.csproj" -c Release -o /app/publish /p:UseAppHost=false

# ====================================================================
# STAGE 2: FINAL - Runtime Nhỏ Gọn
# (Phần này giữ nguyên, không liên quan đến thay đổi trên)
# ====================================================================
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final
WORKDIR /app
COPY --from=publish /app/publish .

COPY Gateway/ocelot.json .
ENV ASPNETCORE_URLS=http://+:80 
EXPOSE 80 
ENTRYPOINT ["dotnet", "ShortenUrl.dll"]