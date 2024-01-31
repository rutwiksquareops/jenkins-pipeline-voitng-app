# because of dotnet, we always build on amd64, and target platforms in cli
# dotnet doesn't support QEMU for building or running. 
# (errors common in arm/v7 32bit) https://github.com/dotnet/dotnet-docker/issues/1537
# https://hub.docker.com/_/microsoft-dotnet
# hadolint ignore=DL3029
# to build for a different platform than your host, use --platform=<platform>
# for example, if you were on Intel (amd64) and wanted to build for ARM, you would use:
# docker buildx build --platform "linux/arm64/v8" .
# Use a default platform if none is specified
ARG BUILDPLATFORM=linux/amd64

# Use a specific version of the SDK
FROM --platform=${BUILDPLATFORM} mcr.microsoft.com/dotnet/sdk:7.0 as build

# Set the target architecture to amd64 if not specified
ARG TARGETARCH=amd64
ARG TARGETPLATFORM

# Print information about the build platforms
RUN echo "I am running on $BUILDPLATFORM, building for $TARGETPLATFORM"

WORKDIR /source
COPY ./worker/*.csproj .
RUN dotnet restore -a $TARGETARCH

COPY ./worker .
RUN dotnet publish -c release -o /app -a $TARGETARCH --self-contained false --no-restore

# App image
FROM mcr.microsoft.com/dotnet/runtime:7.0
WORKDIR /app
COPY --from=build /app .
ENTRYPOINT ["dotnet", "Worker.dll"]
