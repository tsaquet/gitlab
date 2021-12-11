# 1. Install python & pip
FROM python:3.7

# 2. Install pre-commit https://pre-commit.com/
RUN apt-get update && apt-get install -y \
      git && \
      pip install pre-commit requests \
      && mkdir /pre-commit && \
      cd /pre-commit && \
      git init . && \
      git config --global user.email "nobody@nobody.it" && \
      git config --global user.name "Nobo Dy" && \
      pre-commit install

# 3. Add secret check https://github.com/Yelp/detect-secrets
WORKDIR /pre-commit
COPY pre-commit-config.yaml .pre-commit-config.yaml

# 3. Tests!

# 3.1: Just run detect-secrets
## Create a baseline of potential secrets currently found in your git repository.
RUN pip install detect-secrets
RUN detect-secrets scan > .secrets.baseline

# 3.2: Test on existing files (ie no files)
RUN pre-commit run --all-files

# 3.2.1: Add a good file
RUN echo "I'm good code" > goodcode.py && git add goodcode.py && git commit -m "Good code"

# 3.2.2: Add a file with a lot of secrets inside
COPY sample.py sample.py
RUN git add sample.py 
RUN git commit -m "Ooops"
