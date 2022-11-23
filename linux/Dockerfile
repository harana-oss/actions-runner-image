FROM summerwind/actions-runner:latest

RUN curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash \
  && sudo rm -rf /var/lib/apt/lists/*

RUN sudo apt-get update \
  && sudo apt-get install -y \
    gettext-base \
  && sudo rm -rf /var/lib/apt/lists/*

COPY docker-system-prune /etc/arc/hooks/job-completed.d/
