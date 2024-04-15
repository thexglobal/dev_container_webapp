FROM python:3.12-slim

ARG USERNAME=appuser
ARG USER_UID=1001
ARG USER_GID=$USER_UID

RUN set -ex \
    # Create a non-root user
    && groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    # Upgrade the package index and install security upgrades
    && apt-get update && export DEBIAN_FRONTEND=noninteractive \
    # Remove imagemagick due to https://security-tracker.debian.org/tracker/CVE-2019-10131
    && apt-get purge -y imagemagick imagemagick-6-common \
    && apt-get upgrade -y \
    # Clean up
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /code

# Install Python packages
COPY --chown=$USERNAME:$USER_GID requirements.txt /code/
RUN python3 -m pip install --upgrade pip \
    && pip install --no-cache-dir --upgrade --disable-pip-version-check -r requirements.txt \
    && rm requirements.txt

# Copy application code
COPY src/ /code/src/
# Pass in custom VSCode settings and install extensions by default

LABEL devcontainer.metadata='[{ \
  "workspaceFolder": "/code", \
  "remoteUser": "appuser", \ 
}]'

# Ensure proper permissions
RUN chown -R $USERNAME:$USER_GID /code

# Use the non-root user to run the application
## Make sure to reflect new user in PATH
ENV PATH="/home/${USERNAME}/.local/bin:${PATH}"
USER $USERNAME

CMD ["uvicorn", "src.main:app", "--host", "0.0.0.0", "--port", "80"]
