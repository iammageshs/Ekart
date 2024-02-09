# Use a specific version of the OpenJDK Alpine image
FROM openjdk:8u151-jdk-alpine3.7

# Expose port 8070 to the outside world
EXPOSE 8070

# Set environment variable for the application home directory
ENV APP_HOME /usr/src/app

# Create the application directory
RUN mkdir -p $APP_HOME

# Copy the JAR file into the container at $APP_HOME
COPY target/shopping-cart-0.0.1-SNAPSHOT.jar $APP_HOME/app.jar

# Set the working directory to $APP_HOME
WORKDIR $APP_HOME

# Run the application using "exec" for better process management
ENTRYPOINT ["sh", "-c", "exec java -jar app.jar"]
