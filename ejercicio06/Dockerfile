FROM registry.access.redhat.com/redhat-openjdk-18/openjdk18-openshift:1.11-1.1648472891
RUN mkdir -p ocp/deployments
COPY ${PWD}\passwordapi.jar ocp/deployments
EXPOSE 8080
USER 405
CMD ["java","-jar","ocp/deployments/passwordapi.jar"]