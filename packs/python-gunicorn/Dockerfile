FROM python:3.8

RUN apt-get -y update && \
    apt-get -y upgrade && \
    apt-get install -y --no-install-recommends \
    locales

# Set the locale
RUN sed -i 's/^# *\(en_US.UTF-8\)/\1/' /etc/locale.gen \
    && locale-gen
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8
ENV LC_ALL en_US.UTF-8

ENV PORT 8080
EXPOSE 8080

RUN mkdir -p /app
WORKDIR /app

COPY ./oasis /app/oasis
RUN pip3 install -r /app/oasis/requirements.txt

COPY ./requirements.txt /app
RUN pip3 install -r requirements.txt
# call pip install before copying app dependencies
# so unless there is a change in dependencies we would like to use container cache for requirements

COPY ./api /app/api
COPY app.py .
# TODO COPY files to container here if needed

# Creates a non-root user and adds permission to access the /app folder
RUN groupadd -r appuser && useradd -r -s /bin/false -g appuser appuser
RUN chown -R appuser:appuser /app
USER appuser

CMD gunicorn -b 0.0.0.0:8080 -w 3 app:app -t 60