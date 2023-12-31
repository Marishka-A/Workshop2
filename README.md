# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил(а):
- Абросимова Марина Михайловна
- РИ-220931
  
Отметка о выполнении заданий:

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

## Цель работы
Написать программу Hello World на Python с запуском в Jupiter Notebook. Написать программу Hello World на C# с запуском на Unity.

## Задание 1

Ход работы:
- Настроить доступ к google-таблице по api.

Следуя инструкции, сумела настроить доступ:
![2023-10-08_18-55-16](https://github.com/Marishka-A/Workshop2/assets/126682278/56af2d11-e276-4c3b-9708-e82840fe630f)
(Итого подключения)

- Выбрать одну из компьютерных игр, привести скриншот её геймплея и краткое описание концепта игры. Выбрать одну из игровых переменных в игре (ресурсы, внутри игровая валюта, здоровье персонажей и т.д.), описать её роль в игре, условия изменения / появления и диапазон допустимых значений. Построить схему экономической модели в игре и указать место выбранного ресурса в ней.
  
  Игра Genshin Impact - Приключенческая игра с открытым миром. Вы играете за путешественника, который спасает своего близнеца. Геймплей состоит из основного сюжета и множества филлерных историй, бесконечного гринда дял любимой вайфу и кучи разных ивентов. Для экономической модели был выбран примогем (игровая валюта). За примогемы можно получить персонажа, пушку на него, и возможно скинн, если он на него есть конечно.
  ![Для воркшопа2](https://github.com/Marishka-A/Workshop2/assets/126682278/d1375fe6-d7af-4869-a194-0b9ccf5a43cf)
  (Экономическая модель в геншине)


## Задание 2:

Ход работы:
- С помощью скрипта на языке Python заполнить google-таблицу данными, описывающими выбранную игровую переменную в выбранной игре (в качестве таких переменных может выступать игровая валюта, ресурсы, здоровье и т.д.). Средствами google-sheets визуализировать данные в google-таблице (постройте график, диаграмму и пр.) для наглядного представления выбранной игровой величины.

  ![2023-11-02_18-57-56](https://github.com/Marishka-A/Workshop2/assets/126682278/baa7c856-b993-482c-ae36-01b081665aa7)
  ![image](https://github.com/Marishka-A/Workshop2/assets/126682278/81076ada-acf3-4ebe-91a6-59f63261b024)

  
```py
import gspread
import numpy as np
gc = gspread.service_account(filename='unitydatascience2-cfc42e20a895.json')
sh = gc.open("DataSciense")
price = np.random.randint(1, 60, 10)
mon = list(range(1,10))
i = 0
sum = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        if sum >= 160: sum -= 160
        else: sum += price[i-1]
        sh.sheet1.update(('A' + str(i+1)), str(i))
        sh.sheet1.update(('B' + str(i+1)), str(price[i-1]))
        sh.sheet1.update(('C' + str(i+1)), str(sum))
        print(sum)
```

## Задание 3:
Ход работы: 
- Настроить на сцене Unity воспроизведение звуковых файлов, описывающих динамику изменения выбранной переменной.
Мной было выбран звук получения примогема и его траты.

![2023-10-24_13-50-02](https://github.com/Marishka-A/Workshop2/assets/126682278/bb1794ab-e79e-4d09-b4a9-a6286ad6edb8)

  Скрипт:
  ```cs
  using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://docs.google.com/spreadsheets/d/1UJd68gqsV-diyTAy-KkclrfVBan0oSh2MCVBa5zHwk0/edit#gid=0");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}
```

## Выводы

 Во время данной лабораторной работы я научилась заполнять Google Sheets с помощью Python, а также работать со звуками в Unity. Я подключила необходимые сервисы по инструкции, освоила их под свою переменную, заполнила таблицу и использовала эти данные в Unity для воспроизведения звуков при определенных условиях.


## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
