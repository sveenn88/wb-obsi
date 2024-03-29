# Настройки регистров WB-MWAC

#### Карта регистров
*значения по умолчанию*

holding-регистр | Вход | Значение
----- | ------ | ------- 
R9 | S1 | 4
R10 | S2 | 4
R11 | S3 | 4
R12 | F1 | 5
R13 | F1 | 5
R14 | F | 5
#### Допустимые значения holding-регистров
**0** — Ничего не делать
**1** — Выключить
**2** — Включить
**3** — Инвертировать
**4** — Управлять в соответствии с Mapping-матрицей
**5** — Управлять в соответствии с Mapping-матрицей,  через 20 минут повторно имитировать состояние ввода
**6** — Управлять в соответствии с Mapping-матрицей для кнопок (короткие, длинные, двойные и т.д. нажатия)

=============== // =================
#### Карта регистров при значении 4 или 5 holding-регистров
*значения по умолчанию*

Входы | K1 | K2 | Alarm
----- | ----- | ------ | -------
S1 | 384 (9) | 385 (0) | 386 (0)
S2 | 392 (0) | 393 (9) | 394 (0)
S3 | 400 (0) | 401 (0) | 402 (4)
F1 | 408 (4) | 409 (4) | 410 (8)
F2 | 416 (4) | 417 (4) | 418 (8)
F3 | 424 (4) | 425 (4) | 426 (8)
#### Допустимые значения holding-регистров
**0** — нет взаимодействия, вход не управляет выходом вообще
**1** — при выключении входа выход выключается. При включении входа ничего не происходит.
**2** — при выключении входа выход включается
**3** — при выключении входа меняется состояние выхода
**4** — при включении входа выход включается
**5** — при любом изменении состояния входа выход выключается
**6** — при включении входа выход выключается, при выключении входа выход включается
**8** — при включении входа включается выход
**9** — при включении входа выход включается, при выключении входа выход выключается
**10** — при любом изменении состояния входа выход включается
**12** — при включении входа меняется состояния выхода

=============== // =================

#### Короткие нажатия

Вход | K1 | K2 | Alarm
----- | ------ | ------- | ------
S1 | 544 | 545 | 546
S2 | 552 | 553 | 554
S3 | 560 | 561 | 562
F1 | 568 | 569 | 570
F2 | 576 | 577 | 578
F3 | 584 | 585 | 586

#### Длинные нажатия

Вход | K1 | K2 | Alarm
----- | ------ | ------- | ------
S1 | 608 | 609 | 610
S2 | 616 | 617 | 618
S3 | 624 | 625 | 626
F1 | 632 | 633 | 634
F2 | 640 | 641 | 642
F3 | 656 | 657 | 658

#### Двойные нажатия

Вход | K1 | K2 | Alarm
----- | ------ | ------- | ------
S1 | 672 | 673 | 674
S2 | 680 | 681 | 682
S3 | 688 | 689 | 690
F1 | 696 | 697 | 698
F2 | 704 | 705 | 706
F3 | 712 | 713 | 714

#### Короткий затем Длинный

Вход | K1 | K2 | Alarm
----- | ------ | ------- | ------
S1 | 736 | 737 | 738
S2 | 744 | 745 | 746
S3 | 752 | 753 | 754
F1 | 760 | 761 | 762
F2 | 768 | 769 | 770
F3 | 776 | 777 | 778

#### Короткий затем Длинный

Вход | K1 | K2 | Alarm
----- | ------ | ------- | ------
S1 | 736 | 737 | 738
S2 | 744 | 745 | 746
S3 | 752 | 753 | 754
F1 | 760 | 761 | 762
F2 | 768 | 769 | 770
F3 | 776 | 777 | 778
