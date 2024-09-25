# ***Сегодня будем писать игру «Крестики-Нолики» используя сервлеты и JSP.***

https://javarush.com/quests/lectures/jru.module3.lecture03

*Этот проект будет немного отличаться от предыдущих. В нем будут не только задания, но и объяснения как их сделать.<br />
То есть это будет проект из серии «HOW TO …».* <br />

Инструкция:
 1. Сделать fork из репозитория: https://github.com/vasylmalik/project-servlet.git
 2. Скачать свою версию проекта к себе на компьютер.
 3. Настроить запуск приложения в IDEA:
     - Alt + Shift + F9 -> Edit Configurations… -> Alt + insert -> Tomcat -> Local.
     - После этого нужно нажать “CONFIGURE” и указать, куда скачан и распакован архив с Tomcat.
     - Во вкладке “Deployment”: Alt + insert -> Artifact… -> tic-tac-toe:war exploded -> OK.
     - В поле “Application context”: оставить только “/” (слеш).
     - Нажать “APPLY”.
     - Закрыть окно настройки.
     - Сделать первый пробный запуск настроенной конфигурации. Если все сделал правильно – откроется твой браузер по умолчанию, в котором будет:
     ![](https://cdn.javarush.com/images/article/83128ce5-d310-4f23-b1d4-f2d65f21ca68/512.webp)