---
published: true
layout: post
title: 'Легкий обзор Sidekiq'
date: 2016-08-23T05:29:39.000Z
categories: articles
---
## Основы Sidekiq

### Сервер очередей для Rails
Грубо говоря, данный гем позволяет rails порождать новые процессы, которые будут работать в фоновом режиме. Это весьма удобно для обработки громоздких задач (например, считывание данных с большого документа и занесение их в базу данных), чтобы пользователь получил ответ не дожидаясь, пока выполнится вся работа, что делает приложение более отзывчивым. В основе гема стоит [модель акторов](https://ru.wikipedia.org/wiki/%D0%9C%D0%BE%D0%B4%D0%B5%D0%BB%D1%8C_%D0%B0%D0%BA%D1%82%D0%BE%D1%80%D0%BE%D0%B2){:target="_blank"}.
Его установка довольно проста - добавить гем в Gemfile:

	gem 'sidekiq'
    
Запустить в консоли с rails приложением установку нового гема:

	bundle install
	
Указать сервер очередей в config/application.rb:

	config.active_job.queue_adapter = :sidekiq
    
Создать задачу, например:

	rails generate job Some

Что создаст файл app/jobs/some_job.rb с таким содержимым:

	class SomeJob < ApplicationJob
	  queue_as :default

	  def perform(*args)
	    # Do something later
	  end
	end

Для работы Sidekiq нам потребуется установленный и запущенный redis-server. К примеру, на Ubuntu 14.04 она легко устанавливается с помощью команды `sudo apt-get install redis-server`. Запустим sidekiq в dev окружении:

![Sidekiq приветствие](/assets/articles/images/sidekiq-start-message.png)

Теперь мы можем воспользоваться этим в наших контроллерах с помощью:
	
    SomeJob.perform_now(args)

либо

	SomeJob.perform_later(args)
	
Очевидно, в первом случае задача выполняется сразу, во втором - задача отправляется в очередь.  
Но здесь есть свои тонкости.  
Если сознательно не наследовать функционал фреймворка **ActiveJob** (`ApplicationJob` в нашем случае), то воспользоваться этими функциями не удастся.  
В то же время, при добавлении  `include Sidekiq::Worker` станут доступны функции `SomeJob.new.perform(args)` (в этом случае придется создавать объект класса) и `SomeJob.perform_async` соответственно.  
Есть также и отложенные задачи, об этом и о многом другом можно прочитать в [официальном руководстве гема](https://github.com/mperham/sidekiq/wiki/Scheduled-Jobs){:target="_blank"}.