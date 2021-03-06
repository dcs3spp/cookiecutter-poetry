# https://stackoverflow.com/questions/53835198/integrating-python-poetry-with-docker

#
# Use poetry to build wheel and install dependencies into a virtual environment.
# This will store the dependencies during compile docker stage.
# In run stage copy the virtual environment to the final image. This will reduce the 
# image size.
#
# Install poetry using pip, to allow version pinning. Use --ignore-installed to avoid
# dependency conflicts with poetry.
#

# ---------------------------------------------------------------------------------------

##
# base: Configure python environment and set workdir
##
FROM python:{{ cookiecutter.python_version }}-slim as base

ENV PYTHONDONTWRITEBYTECODE=1 \
  PYTHONFAULTHANDLER=1 \
  PYTHONHASHSEED=random \
  PYTHONUNBUFFERED=1
  
WORKDIR /app

# configure user pyuser:
RUN useradd --user-group --create-home --no-log-init --shell /bin/bash pyuser && \
  chown pyuser /app


# ---------------------------------------------------------------------------------------

##
# compile:  Install dependencies from poetry exported requirements
#           Use poetry to build the wheel for the python package.
#           Install the wheel using pip.
##

FROM base as compile

ARG DEPLOY_ENV=development \
  POETRY_VERSION=1.1.7

# pip:
ENV PIP_DEFAULT_TIMEOUT=100 \
  PIP_DISABLE_PIP_VERSION_CHECK=1 \
  PIP_NO_CACHE_DIR=1

# system dependencies:
RUN apt-get update && \
  apt-get install -y --no-install-recommends \
  build-essential gcc && \
  apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false && \
  apt-get clean -y && \
  rm -rf /var/lib/apt/lists/*

# install poetry, ignoring installed dependencies
RUN pip install --ignore-installed "poetry==$POETRY_VERSION" 

# virtual environment:
RUN python -m venv /opt/venv
ENV VIRTUAL_ENV=/opt/venv 
ENV PATH="$VIRTUAL_ENV/bin:$PATH"

# install dependencies:
COPY pyproject.toml poetry.lock ./
RUN /opt/venv/bin/pip install --upgrade pip \
  && poetry install $(if [ "$DEPLY_ENV" = 'production' ]; then echo '--no-dev'; fi) \
    --no-ansi \
    --no-interaction

# copy source:
COPY . .

# build and install wheel:
RUN poetry build && /opt/venv/bin/pip install dist/*.whl

# -------------------------------------------------------------------------------------------

##
# run:    Copy virtualenv from compile stage, to reduce final image size
#         Run the docker-entrypoint.sh script
##

FROM base as run

# Include git hash for traceability of image
ARG GIT_HASH
ENV GIT_HASH=${GIT_HASH:-dev}

# install virtualenv from compiled image
COPY --chown=pyuser:pyuser --from=compile /opt/venv /opt/venv

# set path for virtualenv and set env var so virtualenv is activated
ENV VIRTUAL_ENV="/opt/venv"
ENV PATH="$VIRTUAL_ENV/bin:$PATH"

COPY --chown=pyuser:pyuser ./docker/docker-entrypoint.sh ./docker/wsgi.py ./

USER pyuser

CMD ["./docker-entrypoint.sh"]

