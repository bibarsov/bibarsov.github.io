---
published: true
layout: post
title: 'Легкий обзор Sidekiq'
date: 2016-08-23
categories: ruby-on-rails
---
## Сервер очередей для Rails
Грубо говоря, данный гем позволяет rails порождать новые процессы, которые будут работать в фоновом режиме. Это весьма удобно для обработки громоздких задач (например, считывание данных с большого документа и занесение их в базу данных), чтобы пользователь получил ответ не дожидаясь, пока выполнится вся работа, что делает приложение более отзывчивым. В основе гема стоит [модель акторов](https://ru.wikipedia.org/wiki/%D0%9C%D0%BE%D0%B4%D0%B5%D0%BB%D1%8C_%D0%B0%D0%BA%D1%82%D0%BE%D1%80%D0%BE%D0%B2){:target="_blank"}.
Его установка довольно проста - добавить гем в Gemfile:

{% highlight ruby %}
gem 'sidekiq'
{% endhighlight %}

Запустить в консоли с rails приложением установку нового гема:

{% highlight ruby %}
bundle install
{% endhighlight %}

Указать сервер очередей в config/application.rb:

{% highlight ruby %}
config.active_job.queue_adapter = :sidekiq
{% endhighlight %}

Создать задачу, например:

{% highlight ruby %}
rails generate job Some
{% endhighlight %}

Что создаст файл app/jobs/some_job.rb с таким содержимым:

{% highlight ruby %}
class SomeJob < ApplicationJob
  queue_as :default

  def perform(*args)
    # Do something later
  end
end
{% endhighlight %}

Для работы Sidekiq нам потребуется установленный и запущенный redis-server. К примеру, на Ubuntu 14.04 она легко устанавливается с помощью команды `sudo apt-get install redis-server`. Запустим sidekiq в dev окружении:

![Sidekiq приветствие](/assets/articles/images/sidekiq-start-message.png)

Теперь мы можем воспользоваться этим в наших контроллерах с помощью:
	
{% highlight ruby %}
SomeJob.perform_now(args)
{% endhighlight %}

либо

{% highlight ruby %}
SomeJob.perform_later(args)
{% endhighlight %}

Очевидно, в первом случае задача выполняется сразу, во втором - задача отправляется в очередь.  
Но здесь есть свои тонкости.  
Если сознательно не наследовать функционал фреймворка **ActiveJob** (`ApplicationJob` в нашем случае), то воспользоваться этими функциями не удастся.  
В то же время, при добавлении  `include Sidekiq::Worker` станут доступны функции `SomeJob.new.perform(args)` (в этом случае придется создавать объект класса) и `SomeJob.perform_async` соответственно.  
Есть также и отложенные задачи, об этом и о многом другом можно прочитать в [официальном руководстве гема](https://github.com/mperham/sidekiq/wiki/Scheduled-Jobs){:target="_blank"}.