# Sofa.github.io
<?php
// Проверка отправки формы регистрации
if ($_SERVER == "POST") {
    // Получение и обработка введенных данных
    $fio = htmlspecialchars($_POST);
    $address = htmlspecialchars($_POST);
    $email = htmlspecialchars($_POST);
    $password = htmlspecialchars($_POST);

    // Проверка пароля
    $correct_password = "123456";

    if ($password == $correct_password) {
        // Если пароль верный, перенаправляем на страницу поздравления
        header("Location: congrats.php");
        exit();
    } else {
        // Вывод сообщения о неправильном пароле
        echo "Неверный пароль!";
    }
}
?>


<!DOCTYPE html>
<html>
<head>
    <title>Форма регистрации</title>
</head>
<body>
    <form method="post">
        <p>Ф.И.О.: <input type="text" name="fio"></p>
        <p>Адрес: <input type="text" name="address"></p>
        <p>Email: <input type="email" name="email"></p>
        <p>Пароль: <input type="password" name="password"></p>
        <p><input type="submit" value="Отправить"></p>
    </form>
</body>
</html>


<?php
// Обработка формы отправки письма
if ($_SERVER == "POST") {
    $to = htmlspecialchars($_POST);
    $subject = htmlspecialchars($_POST);
    $message = htmlspecialchars($_POST);

    // Отправка письма
    if (mail($to, $subject, $message)) {
        echo "Письмо успешно отправлено!";
    } else {
        echo "Ошибка при отправке письма.";
    }
}
?>
