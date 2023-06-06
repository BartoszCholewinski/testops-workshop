FROM alpine as builder
WORKDIR /app
RUN echo 123 > output.txt

FROM python:3.12.0b1-slim-buster
WORKDIR /app
COPY --from=builder /app/output.txt .
COPY script.py .
CMD [ "python", "script.py" ]