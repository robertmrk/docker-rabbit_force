# build stage for installing all dependencies and for building wheels
FROM python:3.7.3-stretch AS build

# required pipenv version
ARG RF_PIPENV_VERSION=2018.11.26
# rabbit_force repository URL
ARG RF_REPO_URL=https://github.com/robertmrk/rabbit_force.git
# rabbit_force repostiroy branch
ARG RF_BRANCH_NAME=0.1.1

# change the working directory
WORKDIR /build

# clone the respository
RUN git clone --single-branch --branch ${RF_BRANCH_NAME} --depth 1 ${RF_REPO_URL} . && \
	# install pipenv
	pip install --no-cache-dir pipenv==${RF_PIPENV_VERSION} && \
    # install dependencies from the lock file,
    # create the virtual enviroment in the working directory
    bash -c "PIPENV_VENV_IN_PROJECT=1 pipenv install --deploy --ignore-pipfile"


# final build stage
FROM python:3.7.3-slim-stretch

# user and group configuration
ARG USER_NAME=rabbit_force
ARG GROUP_NAME=rabbit_force
ARG USER_ID=1000
ARG GROUP_ID=1000

# create a user and group
RUN groupadd -g ${GROUP_ID} ${GROUP_NAME} && \
    useradd -g ${GROUP_ID} -u ${USER_ID} ${USER_NAME}

# change the working directory
WORKDIR /app

# copy the application package and virtual enviroment from the build context
COPY --from=build /build/rabbit_force /app/rabbit_force
COPY --from=build /build/.venv /app/.venv

# set the current user and group
USER ${USER_NAME}:${GROUP_NAME}

# execute the application using the virtual enviroment
ENTRYPOINT [".venv/bin/python", "-m", "rabbit_force"]
