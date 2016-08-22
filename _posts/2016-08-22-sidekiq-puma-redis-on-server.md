---
published: false
layout: post
title: 'Настройка Rails, Sidekiq и Puma на Linux/Heroku'
date: 2016-08-22T04:50:39.000Z
categories: articles
---
# Настройка Rails, Sidekiq и Puma на Linux/Heroku
_Оригинальная статья_ - [The Complete Guide: Rails, Sidekiq and Puma on Heroku/Linux Server](http://julianee.com/rails-sidekiq-and-heroku/)

## Введение
Вначале кратко опишем используемые технологии (Sidekiq, Redis, Puma) и задачи, которые они решают. Позже более конкретно опишем их настройку, и то, как обозначить их связь в рамках проекта на Ruby on Rails с помощью Puma.

### Sidekiq
Кратко говоря, данный гем позволяет основному процессу приложения rails (обрабатывающий поступивший от пользователя запрос) породить новые, которые будут работать в фоновом режиме. Это весьма удобно для обработки громоздких задач (например, считывание данных с большого документа и занесение их в базу данных), чтобы пользователь получил ответ не дожидаясь, пока выполнится вся работа, что делает приложение более отзывчивым. В основе гема стоит [модель акторов](https://ru.wikipedia.org/wiki/%D0%9C%D0%BE%D0%B4%D0%B5%D0%BB%D1%8C_%D0%B0%D0%BA%D1%82%D0%BE%D1%80%D0%BE%D0%B2).
Его установка довольно проста - добавить гем в Gemfile

	gem 'sidekiq'
    
Запустить в консоли с rails приложением установку нового гема 

	bundle install
	
Указать сервер очередей в config/application.rb

	config.active_job.queue_adapter = :sidekiq
    
Создать задачу, например

	rails generate job Some

Что создаст файл app/jobs/some_job.rb с таким содержимым

    class SomeJob < ApplicationJob
      queue_as :default

      def perform(*args)
        # Do something later
      end
    end

Теперь мы можем воспользоваться этим в наших контроллерах с помощью
	
    SomeJob.perform_now(args)

Либо

	SomeJob.perform_later(args)
	
В первом случае SomeJob выполнится сразу, не отправляясь в очередь. Во втором функция отработает в фоновом потоке, добавившись в очередь. Есть также и отложенные задачи, об этом можно прочитать в [официальном руководстве гема](https://github.com/mperham/sidekiq/wiki/Scheduled-Jobs).

На высоком уровне Sidekiq следует разделять на две основные части: **Sidekiq Client** и **Sidekiq Server**, который общаются c нереляционной базой данных [**Redis**](https://ru.wikipedia.org/wiki/Redis). 

