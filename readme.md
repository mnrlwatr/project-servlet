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
 4. Открой файл “pom.xml”. В блоке “dependencies” есть 2 зависимости.
    - `javax.servlet-api` отвечает за спецификацию сервлетов. Scope “provided” нужен во время разработки, но не нужен в рантайме (у Tomcat уже есть эта зависимость в папке lib).
    - `jstl` – можно рассматривать как шаблонизатор.
 5. В папке “webapp” есть 3 файла:
    - `index.jsp` – это наш шаблон (аналог HTML страницы). В ней будет разметка и скрипты. Именно файл с названием “index” отдается как начальная страница, если нет каких-либо конфигураций, что мы и увидели в п.3.
    - `/static/main.css` – файл для стилей. Как и в прошлом проекте, здесь все на твое усмотрение, крась как хочешь.
    - `/static/jquery-3.6.0.min.js` – фронтенд зависимость, которую наш сервер будет раздавать как статику.
 6. В пакете “com.tictactoe” будет весь Java-код. Сейчас там есть 2 класса:
    - `Sign` – enum, который отвечает за “крестик/нолик/пустота”.
    - Field – это наше поле. В этом классе есть мапа “field”. Принцип хранения данных будет таким: ячейки поля крестиков-ноликов пронумерованы с нуля. В первой строке 0, 1 и 2. Во второй: 3, 4 и 5. И так далее. Есть также 3 метода. “getEmptyFieldIndex” ищет первую незанятую ячейку (да, соперник у нас будет не очень умным). “checkWin” проверяет не завершена ли игра. Если есть ряд из трех крестиков – возвращает крестик, если из трех ноликов – нолик. Иначе – пустоту. “getFieldData” – возвращает значения мапы “field” в виде списка, отсортированного в порядке возрастания индексов.
 7. Объяснения оп темплиту закончены, теперь можно приступать к выполнению задания. Начнем с того, что отрисуем таблицу 3 на 3. Для этого в “index.jsp” добавь код:
 
```
<table>
	<tr>
		<td>0</td>
		<td>1</td>
		<td>2</td>
	</tr>
	<tr>
		<td>3</td>
		<td>4</td>
		<td>5</td>
	</tr>
	<tr>
		<td>6</td>
		<td>7</td>
		<td>8</td>
	</tr>
</table>
```
Числа в таблице потом уберем и заменим их на крестик, нолик или пустое поле. Также внутри тега “head” подключи файл стилей.<br /> Для этого добавь строку: `<link href="static/main.css" rel="stylesheet">` <br />
Содержимое файла стилей остается на твое усмотрение. Я использовал такой:
```css
td {
border: 3px solid black;
padding: 10px;
border-collapse: separate;
margin: 10px;
width: 100px;
height: 100px;
font-size: 50px;
text-align: center;
empty-cells: show;
}
```
После запуска у меня результат выглядит так: <br />
![](https://cdn.javarush.com/images/article/960b8e58-7f83-4a79-b7c0-a517bbbc1823/512.webp) <br />
8. Теперь давай добавим такой функционал: при клике на ячейку, на сервер будет отправляться запрос, в котором параметром передадим индекс ячейки, по которой был произведен клик. Эту задачу можно разделить на две части: с фронта запрос отправить, на сервере запрос принять. Давай, ради разнообразия, начнем с фронта.
   Давай каждому тегу “d” добавим параметр “onclick”. В значении укажем смену текущей страницы на указанный URL. Сервлет, который будет отвечать за логику, будет иметь URL “/logic”. И будет принимать параметр с названием “click”. Так будем передавать индекс ячейки, по которой кликнул пользователь. <br />
 ```
<table>
    <tr>
        <td onclick="window.location='/logic?click=0'">0</td>
        <td onclick="window.location='/logic?click=1'">1</td>
        <td onclick="window.location='/logic?click=2'">2</td>
    </tr>
    <tr>
        <td onclick="window.location='/logic?click=3'">3</td>
        <td onclick="window.location='/logic?click=4'">4</td>
        <td onclick="window.location='/logic?click=5'">5</td>
    </tr>
    <tr>
        <td onclick="window.location='/logic?click=6'">6</td>
        <td onclick="window.location='/logic?click=7'">7</td>
        <td onclick="window.location='/logic?click=8'">8</td>
    </tr>
</table>
```
Проверить, что все сделано правильно, можно через панель разработчика в браузере. Например, в Chrome она открывается по кнопке F12. В результате клика на ячейку с индексом 4 картина будет такая: <br />
![](https://cdn.javarush.com/images/article/37b6d7b2-1f49-493c-b052-c8fb6bd63784/1080.webp) <br />
Ошибку мы получаем потому, что сервлета, который может отдать сервер по адресу “logic”, мы еще не создали. <br />
9. В пакете “com.tictactoe” создай класс “LogicServlet”, который нужно унаследовать от класса “javax.servlet.http.HttpServlet”. В классе переопредели метод “doGet”.
   И давай добавим метод, который будет получать индекс ячейки, по которой произошел клик. Также нужно добавить маппинг (адрес, по которому этот сервлет будет перехватывать запрос). Предлагаю это делать через аннотацию (но если любишь трудности – можно и через web.xml). Общий код сервлета:
```java
   
package com.tictactoe;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;


@WebServlet(name = "LogicServlet", value = "/logic")
public class LogicServlet extends HttpServlet {
    
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    int index = getSelectedIndex(req);
    resp.sendRedirect("/index.jsp");
}

private int getSelectedIndex(HttpServletRequest request) {
    String click = request.getParameter("click");
    boolean isNumeric = click.chars().allMatch(Character::isDigit);
    return isNumeric ? Integer.parseInt(click) : 0;
    }

}
```
Теперь, при клике по любой ячейке, мы будем на сервере получать индекс этой ячейки (можно убедиться, запустив сервер в дебаге). И будет происходить редирект на эту же страницу, с которой был произведен клик.<br /> 
10. Теперь мы можем кликать, но это еще не игра. Для того, чтоб игра имела логику, нужно сохранять состояние игры (где стоят крестики, где нолики) между запросами. Самый простой метод это сделать – сохранять эти данные в сессии. При таком подходе сессия будет храниться на сервере, а клиент получит идентификатор сессии в куке с именем “JSESSIONID”. Но сессию не нужно создавать каждый раз, а только в начале игры. Давай для этого заведем еще один сервлет, который назовем “InitServlet”. В нем переопределим метод “doGet”, в котором создадим новую сессию, создадим игровое поле, положим это игровое поле и список типа Sign в атрибуты сессии, и отправим “forward” на страницу index.jsp. Код: <br /> 
```java

package com.tictactoe;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;
import java.util.List;
import java.util.Map;

@WebServlet(name = "InitServlet", value = "/start")
public class InitServlet extends HttpServlet {
    
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // Создание новой сессии
        HttpSession currentSession = req.getSession(true);

        // Создание игрового поля
        Field field = new Field();
        Map<Integer, Sign> fieldData = field.getField();

        // Получение списка значений поля
        List<Sign> data = field.getFieldData();

        // Добавление в сессию параметров поля (нужно будет для хранения состояния между запросами)
        currentSession.setAttribute("field", field);
        // и значений поля, отсортированных по индексу (нужно для отрисовки крестиков и ноликов)
        currentSession.setAttribute("data", data);

        // Перенаправление запроса на страницу index.jsp через сервер
        getServletContext().getRequestDispatcher("/index.jsp").forward(req, resp);
    }
}
``` 
И чтоб не забыть, давай стартовую страницу, которая открывается в браузере после запуска сервера, сменим на “/start”: <br />
![](https://cdn.javarush.com/images/article/b2ad55cf-7d01-4938-a468-e1f647fb6238/800.webp) <br />
Теперь после перезапуска сервера и клика по любой ячейке поля в меню разработчика браузера в секции “Request Headers” будет присутствовать кука с идентификатором сессии: <br />
![](https://cdn.javarush.com/images/article/ca94ddd6-cc0f-4356-a0c3-c8985d64587d/1080.webp) <br />
11. Когда у нас появилось хранилище, в котором мы можем хранить состояние между запросами с клиента (браузера), можно начинать писать логику игры. Логика у нас находится в “LogicServlet”. Работать нам нужно с методом “doGet”. Давай в метод добавим такое поведение:
    - получим объект “field” типа Field из сессии (вынесем в метод “extractField”).
    - поставим крестик там, где кликнул пользователь (пока что без каких-либо проверок). <br />
```java

@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // Получаем текущую сессию
    HttpSession currentSession = req.getSession();

    // Получаем объект игрового поля из сессии
    Field field = extractField(currentSession);

    // получаем индекс ячейки, по которой произошел клик
    int index = getSelectedIndex(req);

    // ставим крестик в ячейке, по которой кликнул пользователь
    field.getField().put(index, Sign.CROSS);

    // Считаем список значков
    List<Sign> data = field.getFieldData();

    // Обновляем объект поля и список значков в сессии
    currentSession.setAttribute("data", data);
    currentSession.setAttribute("field", field);

    resp.sendRedirect("/index.jsp");
}



private Field extractField(HttpSession currentSession) {
    Object fieldAttribute = currentSession.getAttribute("field");
    if (Field.class != fieldAttribute.getClass()) {
        currentSession.invalidate();
        throw new RuntimeException("Session is broken, try one more time");
    }
    return (Field) fieldAttribute;
}
```
Поведение пока не изменилось, но если запустить сервер в дебаге и поставить брейкпоинт на строке, где шлется редирект, можно посмотреть “внутренности” объекта “data”. Там действительно появляется “CROSS” под индексом, по котором был клик. <br />
12. Теперь время отобразить крестик на фронтенде. Для этого поработаем с файлом “index.jsp” и технологией “JSTL”.
    - В секции <head> добавим:`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>`
    - В таблице внутри каждого блока <td> поменяем индекс на конструкцию, которая позволяет вычислять значения. Например, для индекса ноль: `<td onclick="window.location='/logic?click=0'">${data.get(0).getSign()}</td>` Теперь при клике на ячейку там будет появляться крестик: <br />
      ![](https://cdn.javarush.com/images/article/190bd8aa-8c1b-495c-8aaf-2a595e42058c/512.webp) <br />
13. Мы свой ход сделали, теперь очередь за “ноликом”. И добавим парочку проверок здесь же, чтоб знаки не ставились в уже занятые клетки.
    - Нужно проверить, что ячейка, по которой был клик пустая. Иначе ничего не делаем и отправляем пользователя на ту же страницу без изменений параметров сессии.
    - Так как количество клеток на поле нечетное, возможна ситуация, когда крестик удалось поставить, а для нолика уже нет места. Поэтому, после того как поставили крестик, пытаемся получить индекс незанятой ячейки (метод getEmptyFieldIndex класса Field). Если индекс не отрицательный, тогда ставим туда нолик. <br />
      ![](https://cdn.javarush.com/images/article/29c1b5b8-762a-4f48-9a0f-37ee498670e8/512.webp) <br />
 ```java 
package com.tictactoe;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;
import java.util.List;

@WebServlet(name = "LogicServlet", value = "/logic")
public class LogicServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // Получаем текущую сессию
        HttpSession currentSession = req.getSession();

        // Получаем объект игрового поля из сессии
        Field field = extractField(currentSession);

        // получаем индекс ячейки, по которой произошел клик
        int index = getSelectedIndex(req);
        Sign currentSign = field.getField().get(index);

        // Проверяем, что ячейка, по которой был клик пустая.
        // Иначе ничего не делаем и отправляем пользователя на ту же страницу без изменений
        // параметров в сессии
        if (Sign.EMPTY != currentSign) {
            RequestDispatcher dispatcher = getServletContext().getRequestDispatcher("/index.jsp");
            dispatcher.forward(req, resp);
            return;
        }

        // ставим крестик в ячейке, по которой кликнул пользователь
        field.getField().put(index, Sign.CROSS);

        // Получаем пустую ячейку поля
        int emptyFieldIndex = field.getEmptyFieldIndex();

        if (emptyFieldIndex >= 0) {
            field.getField().put(emptyFieldIndex, Sign.NOUGHT);
        }

        // Считаем список значков
        List<Sign> data = field.getFieldData();

        // Обновляем объект поля и список значков в сессии
        currentSession.setAttribute("data", data);
        currentSession.setAttribute("field", field);

        resp.sendRedirect("/index.jsp");
    }

    private int getSelectedIndex(HttpServletRequest request) {
        String click = request.getParameter("click");
        boolean isNumeric = click.chars().allMatch(Character::isDigit);
        return isNumeric ? Integer.parseInt(click) : 0;
    }

    private Field extractField(HttpSession currentSession) {
        Object fieldAttribute = currentSession.getAttribute("field");
        if (Field.class != fieldAttribute.getClass()) {
            currentSession.invalidate();
            throw new RuntimeException("Session is broken, try one more time");
        }
        return (Field) fieldAttribute;
    }
}
``` 
14. На данном этапе можно ставить крестики, AI отвечает ноликами. Но нет проверки, когда стоит остановить игру. Это может быть в трех случаях:
    - после очередного хода крестика образовалась линия из трех крестиков;
    - после очередного ответного хода ноликом образовалась линия из трех ноликов;
    - после очередного хода крестика закончились пустые клетки. <br />
Добавим метод, который проверяет, нет ли трех крестиков/ноликов в ряд:
 ```java
/**
 * Метод проверяет, нет ли трех крестиков/ноликов в ряд.
 * Возвращает true/false
 */
private boolean checkWin(HttpServletResponse response, HttpSession currentSession, Field field) throws IOException {
    Sign winner = field.checkWin();
    if (Sign.CROSS == winner || Sign.NOUGHT == winner) {
        // Добавляем флаг, который показывает что кто-то победил
        currentSession.setAttribute("winner", winner);

        // Считаем список значков
        List<Sign> data = field.getFieldData();

        // Обновляем этот список в сессии
        currentSession.setAttribute("data", data);

        // Шлем редирект
        response.sendRedirect("/index.jsp");
        return true;
    }
    return false;
}
```
Особенность этого метода в том, что если победитель нашелся – добавляем в сессию еще параметр, используя который, изменим отображение в “index.jsp” в последующих пунктах.
15. Добавим в метод “doGet” два раза вызов метода “checkWin”. Первый раз после установки крестика, второй – после установки нолика.
```java
// Проверяем, не победил ли крестик после добавления последнего клика пользователя
if (checkWin(resp, currentSession, field)) {
    return;
}
```

```java
if (emptyFieldIndex >= 0) {
    field.getField().put(emptyFieldIndex, Sign.NOUGHT);
    // Проверяем, не победил ли нолик после добавление последнего нолика
    if (checkWin(resp, currentSession, field)) {
        return;
    }
}
```
16. По поведению почти ничего не изменилось (кроме того, что в случае победы одного из знаков перестают ставиться нолики. Давай в “index.jsp” используем параметр “winner” и выведем победителя. Используем директивы `c:set` и `c:if` после таблицы: <br />
```
<hr>
<c:set var="CROSSES" value="<%=Sign.CROSS%>"/>
<c:set var="NOUGHTS" value="<%=Sign.NOUGHT%>"/>

<c:if test="${winner == CROSSES}">
    <h1>CROSSES WIN!</h1>
</c:if>
<c:if test="${winner == NOUGHTS}">
    <h1>NOUGHTS WIN!</h1>
</c:if>
```
Если выигрывают крестики, то выведется сообщение “CROSSES WIN!”, если нолики – “NOUGHTS WIN!”. В результате можем получить одну из двух надписей: <br />
![](https://cdn.javarush.com/images/article/20218ef6-d6b8-4697-bab9-18d1c71f238e/800.webp) <br />
17. Если есть победитель, нужно иметь возможность взять реванш. Для этого нужна кнопка, которая отправит на сервер запрос. А сервер инвалидирует текущую сессию и перенаправит запрос снова на “/start”.


   В “index.jsp” в секции “head” пропишем скрипт “jquery”. Используя эту библиотеку, мы будем отправлять запрос на сервер.

```
<script src="<c:url value="/static/jquery-3.6.0.min.js"/>"></script>
``` 
   В “index.jsp” в секции “script” добавим функцию, которая умеет оправлять POST запрос на сервер. Функцию сделаем синхронной, и когда придет ответ с сервера – перезагрузит текущую страницу.
```
<script>
    function restart() {
        $.ajax({
            url: '/restart',
            type: 'POST',
            contentType: 'application/json;charset=UTF-8',
            async: false,
            success: function () {
                location.reload();
            }
        });
    }
</script>
``` 
   Внутри блоков “c:if” добавим кнопку, при клике на которую вызывается только что написанная функция:
```
<c:if test="${winner == CROSSES}">
    <h1>CROSSES WIN!</h1>
    <button onclick="restart()">Start again</button>
</c:if>
<c:if test="${winner == NOUGHTS}">
    <h1>NOUGHTS WIN!</h1>
    <button onclick="restart()">Start again</button>
</c:if>
``` 
   Создадим новый сервлет, который будет обслуживать URL “/restart”.
```java
package com.tictactoe;

import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(name = "RestartServlet", value = "/restart")
public class RestartServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws IOException {
        req.getSession().invalidate();
        resp.sendRedirect("/start");
    }
}
```
   После победы появится кнопка “Start again”. После клика по ней – поле полностью очистится, и игра начнется сначала. <br />
   ![](https://cdn.javarush.com/images/article/b323597b-bedd-46ce-a0e1-d66bda1747a9/800.webp) <br />
18. Осталось рассмотреть последнюю ситуацию. Что, если крестик пользователь поставил, победы не произошло, и для нолика нету места? Тогда это ничья, именно ее сейчас и обработаем:


   В “LogicServlet” в сессию добавим еще один параметр “draw”, обновим поле “data” и отправим редирект на “index.jsp”:
 ```java
// Если такая ячейка присутствует
if (emptyFieldIndex >= 0) {
    …
}
// Если пустой ячейки нет и никто не победил - значит это ничья
else {
    // Добавляем в сессию флаг, который сигнализирует что произошла ничья
    currentSession.setAttribute("draw", true);

    // Считаем список значков
    List<Sign> data = field.getFieldData();

    // Обновляем этот список в сессии
    currentSession.setAttribute("data", data);

    // Шлем редирект
    response.sendRedirect("/index.jsp");
    return;
}
```
   В “index.jsp” обработаем этот параметр:
```
<c:if test="${draw}">
    <h1>IT'S A DRAW</h1>
    <br>
    <button onclick="restart()">Start again</button>
</c:if>
```
   В результате ничьей получим соответствующее сообщение и предложение начать сначала: <br />
   ![](https://cdn.javarush.com/images/article/1d9847b6-3a6c-4b50-955d-2ef167708dc8/512.webp) <br />


   На этом написание игры закончено. 

# Код классов и файлов, с которыми работали

***InitServlet***

```java
package com.tictactoe;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;
import java.util.List;
import java.util.Map;

@WebServlet(name = "InitServlet", value = "/start")
public class InitServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // Создание новой сессии
        HttpSession currentSession = req.getSession(true);

        // Создание игрового поля
        Field field = new Field();
        Map<Integer, Sign> fieldData = field.getField();

        // Получение списка значений поля
        List<Sign> data = field.getFieldData();

        // Добавление с сессию параметров поля (нужно будет для хранения состояния между запросами)
        currentSession.setAttribute("field", field);
        // и значений поля, отсортированных по индексу (нужно для отрисовки крестиков и ноликов)
        currentSession.setAttribute("data", data);

        // Перенаправление запроса на страницу index.jsp через сервер
        getServletContext().getRequestDispatcher("/index.jsp").forward(req, resp);
    }
}
```

***LogicServlet***

```java
package com.tictactoe;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;
import java.util.List;

@WebServlet(name = "LogicServlet", value = "/logic")
public class LogicServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // Получаем текущую сессию
        HttpSession currentSession = req.getSession();

        // Получаем объект игрового поля из сессии
        Field field = extractField(currentSession);

        // получаем индекс ячейки, по которой произошел клик
        int index = getSelectedIndex(req);
        Sign currentSign = field.getField().get(index);

        // Проверяем, что ячейка, по которой был клик пустая.
        // Иначе ничего не делаем и отправляем пользователя на ту же страницу без изменений
        // параметров в сессии
        if (Sign.EMPTY != currentSign) {
            RequestDispatcher dispatcher = getServletContext().getRequestDispatcher("/index.jsp");
            dispatcher.forward(req, resp);
            return;
        }

        // ставим крестик в ячейке, по которой кликнул пользователь
        field.getField().put(index, Sign.CROSS);

        // Проверяем, не победил ли крестик после добавление последнего клика пользователя
        if (checkWin(resp, currentSession, field)) {
            return;
        }

        // Получаем пустую ячейку поля
        int emptyFieldIndex = field.getEmptyFieldIndex();

        if (emptyFieldIndex >= 0) {
            field.getField().put(emptyFieldIndex, Sign.NOUGHT);
            // Проверяем, не победил ли нолик после добавление последнего нолика
            if (checkWin(resp, currentSession, field)) {
                return;
            }
        }
        // Если пустой ячейки нет и никто не победил - значит это ничья
        else {
            // Добавляем в сессию флаг, который сигнализирует что произошла ничья
            currentSession.setAttribute("draw", true);

            // Считаем список значков
            List<Sign> data = field.getFieldData();

            // Обновляем этот список в сессии
            currentSession.setAttribute("data", data);

            // Шлем редирект
            resp.sendRedirect("/index.jsp");
            return;
        }

        // Считаем список значков
        List<Sign> data = field.getFieldData();

        // Обновляем объект поля и список значков в сессии
        currentSession.setAttribute("data", data);
        currentSession.setAttribute("field", field);

        resp.sendRedirect("/index.jsp");
    }

    /**
     * Метод проверяет, нет ли трех крестиков/ноликов в ряд.
     * Возвращает true/false
     */
    private boolean checkWin(HttpServletResponse response, HttpSession currentSession, Field field) throws IOException {
        Sign winner = field.checkWin();
        if (Sign.CROSS == winner || Sign.NOUGHT == winner) {
            // Добавляем флаг, который показывает что кто-то победил
            currentSession.setAttribute("winner", winner);

            // Считаем список значков
            List<Sign> data = field.getFieldData();

            // Обновляем этот список в сессии
            currentSession.setAttribute("data", data);

            // Шлем редирект
            response.sendRedirect("/index.jsp");
            return true;
        }
        return false;
    }

    private int getSelectedIndex(HttpServletRequest request) {
        String click = request.getParameter("click");
        boolean isNumeric = click.chars().allMatch(Character::isDigit);
        return isNumeric ? Integer.parseInt(click) : 0;
    }

    private Field extractField(HttpSession currentSession) {
        Object fieldAttribute = currentSession.getAttribute("field");
        if (Field.class != fieldAttribute.getClass()) {
            currentSession.invalidate();
            throw new RuntimeException("Session is broken, try one more time");
        }
        return (Field) fieldAttribute;
    }
}

```

***RestartServlet***

```java
package com.tictactoe;

import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(name = "RestartServlet", value = "/restart")
public class RestartServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws IOException {
        req.getSession().invalidate();
        resp.sendRedirect("/start");
    }
}

```

***index.jsp***

```html
<%@ page import="com.tictactoe.Sign" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>

<!DOCTYPE html>
<html>
<head>
    <link href="static/main.css" rel="stylesheet">
    <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
    <script src="<c:url value="/static/jquery-3.6.0.min.js"/>"></script>
    <title>Tic-Tac-Toe</title>
</head>
<body>
<h1>Tic-Tac-Toe</h1>

<table>
    <tr>
        <td onclick="window.location='/logic?click=0'">${data.get(0).getSign()}</td>
        <td onclick="window.location='/logic?click=1'">${data.get(1).getSign()}</td>
        <td onclick="window.location='/logic?click=2'">${data.get(2).getSign()}</td>
    </tr>
    <tr>
        <td onclick="window.location='/logic?click=3'">${data.get(3).getSign()}</td>
        <td onclick="window.location='/logic?click=4'">${data.get(4).getSign()}</td>
        <td onclick="window.location='/logic?click=5'">${data.get(5).getSign()}</td>
    </tr>
    <tr>
        <td onclick="window.location='/logic?click=6'">${data.get(6).getSign()}</td>
        <td onclick="window.location='/logic?click=7'">${data.get(7).getSign()}</td>
        <td onclick="window.location='/logic?click=8'">${data.get(8).getSign()}</td>
    </tr>
</table>

<hr>
<c:set var="CROSSES" value="<%=Sign.CROSS%>"/>
<c:set var="NOUGHTS" value="<%=Sign.NOUGHT%>"/>

<c:if test="${winner == CROSSES}">
    <h1>CROSSES WIN!</h1>
    <button onclick="restart()">Start again</button>
</c:if>
<c:if test="${winner == NOUGHTS}">
    <h1>NOUGHTS WIN!</h1>
    <button onclick="restart()">Start again</button>
</c:if>
<c:if test="${draw}">
    <h1>IT'S A DRAW</h1>
    <button onclick="restart()">Start again</button>
</c:if>

<script>
    function restart() {
        $.ajax({
            url: '/restart',
            type: 'POST',
            contentType: 'application/json;charset=UTF-8',
            async: false,
            success: function () {
                location.reload();
            }
        });
    }
</script>

</body>
</html>
```

***main.css***

```css
td {
    border: 3px solid black;
    padding: 10px;
    border-collapse: separate;
    margin: 10px;
    width: 100px;
    height: 100px;
    font-size: 50px;
    text-align: center;
    empty-cells: show;
   }
```



