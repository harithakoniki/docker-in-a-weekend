# ums-v6 - 690MB
# First stage: Use the official Tomcat image with OpenJDK 21 for building the project
FROM tomcat:10.1.31-jdk21-temurin-noble AS builder

# Set the frontend to non-interactive to avoid prompts
ENV DEBIAN_FRONTEND=noninteractive

# Install Maven in a single RUN command
RUN apt-get update && \
    apt-get install --no-install-recommends -y \
    maven \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Set the working directory
WORKDIR /app

# Copy the Maven project files to the container
COPY pom.xml .
COPY src ./src

# Build the project and skip tests
RUN mvn clean package -DskipTests	

# Second stage: Use a lighter Tomcat image with JRE for runtime
FROM tomcat:10.1.31-jre21-temurin-noble AS final

# Set the frontend to non-interactive to avoid prompts
ENV DEBIAN_FRONTEND=noninteractive

# Install all required packages in a single RUN command
RUN apt-get update && \
    apt-get install --no-install-recommends -y \
    dnsutils \
    iputils-ping \
    telnet \
    curl \
    mysql-client \
    && apt-get clean && rm -rf /var/lib/apt/lists/*	
	
# Remove default web applications from Tomcat
RUN rm -rf /usr/local/tomcat/webapps/*

# Copy the WAR file from the builder stage to the Tomcat webapps directory
COPY --from=builder /app/target/usermgmt-webapp.war /usr/local/tomcat/webapps/ROOT.war

# Expose the default Tomcat port
EXPOSE 8080

# Start Tomcat server
CMD ["catalina.sh", "run"]
