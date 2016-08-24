---
published: true
layout: post
title: Google URL Shortener. Небольшой туториал
date: 2016-08-24T20:02:39.000Z
categories: articles
---
## Google URL Shortener и Ruby

### Зачем?
Гугл предоставляет весьма удобное API для сжатия ссылок. Короткая ссылка является неким "посредником" в процессе передачи пользователю истинного местоположения ресурса. В этом есть свои преимущества: URL адрес  обретает лаконичный вид, а также собирает информацию о пользователе, перешедшем по нему (здесь есть нюансы, о которых я напишу дальше).

### Хау-ту
Воспользоваться API очень просто. Достаточно перейти [сюда](https://goo.gl/){:target="_blank"} и вставить свою ссылку.  
Лично меня данный способ не устраивал, т.к. для меня формирование ссылок автоматически было приоритетной задачей.  
Для получения короткого URL достаточно использовать следующую команду в cURL-cli:


    curl https://www.googleapis.com/urlshortener/v1/url \  
    -H 'Content-Type: application/json' \  
    -d '{"longUrl": "http://www.google.com/"}'


В [официальной документации к API](https://developers.google.com/url-shortener/v1/getting_started) сказано, что для получения увеличенного лимита запросов в день требуется создать API ключ и передавать его в теле запроса. Стоит заметить, что передача ключа в запросе позволяет идентифицировать пользователя, создавшего короткую ссылку.
Для использования API в полной мере следует:  
- Создать проект в [консоли управления проектами](https://console.developers.google.com){:target="_blank"}
- Найти URL Shortener API в библиотеке API

![Google Library Pic](/assets/articles/images/google-library.png)

- Подключить URL Shortener API 
- Создать новый API ключ

Теперь мы можем использовать его для автоматической генерации коротких ссылок.  
Для этого я написал небольшой скрипт на Ruby:


    require 'net/http'
    require 'json'

    url = "https://www.googleapis.com/urlshortener/v1/url?key=YOUR_API_KEY"
    http = Net::HTTP.new(uri.host, uri.port)
    http.use_ssl = true
    request = Net::HTTP::Post.new(uri.request_uri,  'Content-Type' => 'application/json')
    request.body = {longUrl: "http://www.example.com"}.to_json

    json_response = http.request(request)
    response = JSON.parse json_response.body
    SHORT_URL = response['id']


