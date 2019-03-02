---
title: "Скрипт для перемещения заблокированных пользователей  Active Directory "
date: 2018-09-15T09:33:58Z
tags: ["Power Shell", "Active Directory","Системное администрирование"]
---
Скрипт достаточно незамысловатый. Лежит на сервере, где крутится контроллер домена. К контроллеру подключена хранилка по smb или iscsi. Сначала получаем массивом список пользователей на определенном терминальном сервере сервере, сравниваем с заблокированными, перемещаем. Всё просто и празаично


{{< highlight ps1 >}}
#Получение массива пользователей исходя из папки  users на определенном терминальном сервере
$arr1 = Get-ChildItem '\\192.168.0.31\C$\Users' |
       Where-Object {$_.PSIsContainer} |
       Foreach-Object {$_.Name}

# получение списка заблокированных пользоватлей
$blocked = Search-ADAccount -AccountDisabled  
$blocked =  $blocked.SamAccountName
# просто вложенные циклы которые проверяют принадлежность заблокированных пользоватлей в терминале и переносят их на диск
foreach ($Blockeduser in $blocked) {
  foreach ($WhiteUser in $arr1) {
    if ($Blockeduser -like $WhiteUser)
    {
    mkdir D:\TRM1\$Blockeduser
    Robocopy.exe \\192.168.0.31\C$\Users\$Blockeduser D:\TRM\$Blockeduser /MOVE /E /COPYALL /xj /xf *.TMP /xf *.lst #Перемещаем
    }
   }



{{< /highlight >}}
