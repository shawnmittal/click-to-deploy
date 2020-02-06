include ../crd.Makefile
include ../gcloud.Makefile
include ../var.Makefile
include ../images.Makefile

CHART_NAME := drupal
APP_ID ?= $(CHART_NAME)

TRACK ?= 8.8

DRUPAL_EXPORTER_TAG ?= exporter
METRICS_EXPORTER_TAG ?= v0.5.1

VERIFY_WAIT_TIMEOUT = 1800

SOURCE_REGISTRY ?= marketplace.gcr.io/google
IMAGE_DRUPAL ?= $(SOURCE_REGISTRY)/drupal8-php7-apache:$(TRACK)
IMAGE_MARIADB ?= $(SOURCE_REGISTRY)/mariadb10:latest
IMAGE_APACHE_EXPORTER ?= $(SOURCE_REGISTRY)/wordpress/apache-exporter:latest
IMAGE_MYSQL_EXPORTER ?= $(SOURCE_REGISTRY)/wordpress/mysqld-exporter:latest
IMAGE_PROMETHEUS_TO_SD ?= k8s.gcr.io/prometheus-to-sd:$(METRICS_EXPORTER_TAG)


# Main image
image-$(CHART_NAME) := $(call get_sha256,$(IMAGE_DRUPAL))

# List of images used in application
ADDITIONAL_IMAGES := mariadb apache-exporter prometheus-to-sd mysqld-exporter

# Additional images variable names should correspond with ADDITIONAL_IMAGES list
image-mariadb := $(call get_sha256,$(IMAGE_MARIADB))
image-apache-exporter := $(call get_sha256,$(IMAGE_APACHE_EXPORTER))
image-prometheus-to-sd := $(call get_sha256,$(IMAGE_PROMETHEUS_TO_SD))
image-mysqld-exporter := $(call get_sha256,$(IMAGE_MYSQL_EXPORTER))

C2D_CONTAINER_RELEASE := $(call get_c2d_release,$(image-$(CHART_NAME)))

BUILD_ID := $(shell date --utc +%Y%m%d-%H%M%S)
RELEASE ?= $(C2D_CONTAINER_RELEASE)-$(BUILD_ID)
NAME ?= $(APP_ID)-1

# Additional variables
ifdef PUBLIC_SERVICE_AND_INGRESS_ENABLED
  PUBLIC_SERVICE_AND_INGRESS_ENABLED_FIELD = , "enablePublicServiceAndIngress": $(PUBLIC_SERVICE_AND_INGRESS_ENABLED)
endif

ifdef METRICS_EXPORTER_ENABLED
  METRICS_EXPORTER_ENABLED_FIELD = , "metrics.enabled": $(METRICS_EXPORTER_ENABLED)
endif

APP_PARAMETERS ?= { \
  "name": "$(NAME)", \
  "namespace": "$(NAMESPACE)" \
  $(PUBLIC_SERVICE_AND_INGRESS_ENABLED_FIELD) \
  $(METRICS_EXPORTER_ENABLED_FIELD) \
}

# c2d_deployer.Makefile provides the main targets for installing the application.
# It requires several APP_* variables defined above, and thus must be included after.
include ../c2d_deployer.Makefile


# Build tester image
app/build:: .build/$(CHART_NAME)/tester