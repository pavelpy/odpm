# odpm

Основная задача данного проекта - это упростить жизнь разработчику при работе с системой odoo. Данный проект отражает ислючительно мой собственный опыт и лично мой взгляд на удобство разработки.

# Идея
В процессе работы с платформой odoo у меня начали появляться рутинные задачи, которые требуют большой усидчивости и внимания. Ситуацию усугбляет то, что отладка и отлов ошибок в них очень сильно затруднен. Например мне нужно создать новый проект, для этого мне нужно подготовить окружение с учетом версий не только odoo но и самого python. А если мне нужно проект сделать совместимым с несколькими версиями то совсем тяжко. И очень часто возникала ситуация когда трудоемкость самого модуля сильно меньше чем затраты не создание окружения и прочих вспомогательных операций. Со временем я осознал что действую в этих ситуациях по определенному шаблону и пришло понимание того, что эти действия необходимо автоматизировать. Вот несколько операций которые вы можете совершать с помощью данного инструмента:
- Создать рабочее окружение для проекта
- Создать архив базы
- Сделать сброс базы
- Восттановить базу из архива
- ...
Так же часть ресурсов разработчика расходуется на упорядочение зависмостей, которые реализованы третьей стороной, например проект muk. Т.е. если бы у нас был инструмент, который бы мог сам скачать сторонние проекты и подключить их к нашему, то это так же сэкономило бы много нервов.

# Работа с проектом
```note
В связи с тем, что чаще всего мне требовалось подхватывать уже существующий проект, то на данный момент автоматизированное развертывание окружение возможно только при наличии git репозитория уже сущствующиего проекта, содержащего odoo модули. Репозиторий должен иметь ветки с именами версий аналогично проекту odoo. Инициализация проекта пока в разработке.
```
Выбирем или создаем каталог, где будет располагаться наш проект и создаем .json файл со следующим содержимым:
```json
{
    "projects_dir": "/home/my_home_dir/projects/",
    "odoo_projects_dir": "/home/www-data/odoo_projects/",
    "odoo_dir": "/home/www-data/odoo/",
    "odoo_venvs_dir": "/home/www-data/odoo_venvs/",
    "dependencies_projects_urls": [],
    "dev_project_url": "https://yartsev@bitbucket.org/yartsevco/solid_pos.git",
    "odoo_version_for_project": "11.0",
    "project_name": "oodobno",
    "modules_to_update":["solid_pos"],
    "git_servers_params":{
         "bitbucket.org":{
            "user":"your_login",
            "password":"your_password"
        }
    }
    "database_name":"new_db"
}
```
Теперь остановимся на наждом параметре подробнее:
- projects_dir - каталог с проектами, в данном случаем проект может являть собой не только модули odoo, а нечто большее, так же у меня проект хранит файл конфигурации odoo, и файл запуска odoo в режиме разработки, для каждого проекта они уникальны. Необязательный параметр, по умолчанию будет создан каталог в домашней директории пользователя от имени которого запускается приложение. Тип - строка
- odoo_projects_dir - каталог в который загружаются все проекты которые являют собой либо модуль odoo либо несколько модулей, эти проект должны иметь ветки с именами совподающими с версиями odoo. Необязательный параметр, по умолчанию будет создан каталог в домашней директории пользователя от имени которого запускается приложение. Тип - строка
- odoo_dir - каталог, в который будет склонирована сама odoo из своего официального репозитория. Необязательный параметр, по умолчанию будет создан каталог в домашней директории пользователя от имени которого запускается приложение. Тип - строка
- odoo_venvs_dir - каталог в котором содрежатся виртуальные окружения используемых разработчиком версий odoo. Необязательный параметр, по умолчанию будет создан каталог в домашней директории пользователя от имени которого запускается приложение. Тип - строка
- dependencies_projects_urls - список проектов созданных третьими лицами (например OCA или Muk). Обязательный параметр. Тип - массив.
- dev_project_url - ссылка на текущий разрабатываемый проект. Обязательный параметр. Тип - строка
- odoo_version_for_project - имя версии odoo для проекта. Обязательный параметр. Тип - строка.
- project_name - имя текущего проекта, можно не указывать. Если указываете то должно совпадать с именем каталога в котором находится проект. Обязательный параметр. Тип - строка.
- git_servers_params -  можно указать сервера и логин с паролем для него, чтобы odpm при работе с закрытыми репозиториями не спрашивал пароли, но данное решение не является безопасным, используете на свой страх и риск. Обязательный параметр. Тип - объект.
- database_name - имя базы данных, которое будет использовано при разработке. Необязательный параметр, по умолчанию будет создана баз с именем проекта с прибавлениме версии odoo. Такой подход позволяет быстро переключаться между версий системы. Тип - строка.
- modules_to_update -  список модулей, который будет установлен при первом запуске базы или автоматически обновлен при перезапуске. Обязательный параметр. Тип - массив.

# Параметры коммандной строки

* --drop-db - укажите через пробел имя базы данных для сброса
* --backup-db - используется совместно с параметром --arch-path, позволяет сделать архив базы данных
* --restore-db - используется совместно с параметром --arch-path, позволяет восстановить архив базы данных
* --arch-path - путь к архиву базы данных, используется для создания и восстановления архивов баз
* --update -  делает  git pull во всех репозиториях odoo и проектах с модулями odoo
* --project-path - данный парметр позволяет запустить odpm в контесте другого проекта
* --dev-restart - запустит odoo с вашим проектом выводом консоли
* --create-module - создаст заготовку модуля из шаблона, в отличии от стандартного в шаблоне присутствует часть фронтэнда

 Дорожная карта:
 - сброс базы +
 - бекап базы +
 - восстановление базы +
 - создание модуля odoo с файлами для фронтэнда +
 - обновить гит репозитории +
 - принудительная пересборка окружения virtualenv -
 - инициализазия проекта -
 - нужно добавить сбор ключей для гит серверов - ???

# История изменений
*****************

* **Release 0.0.1 (2020-01-03)**

  * Первый релиз