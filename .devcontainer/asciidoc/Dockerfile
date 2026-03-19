FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

# Install Ruby, Java (for PlantUML), Graphviz (for diagram rendering), and build tools
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       curl \
       git \
       bash \
       ruby \
       ruby-dev \
       build-essential \
       default-jre-headless \
       graphviz \
       plantuml \
    && rm -rf /var/lib/apt/lists/*

# Install Asciidoctor, PDF export, diagram support, and syntax highlighting
RUN gem install \
    asciidoctor \
    asciidoctor-pdf \
    asciidoctor-diagram \
    asciidoctor-diagram-plantuml \
    rouge

# Verify installations
RUN asciidoctor --version && asciidoctor-pdf --version

WORKDIR /workspace