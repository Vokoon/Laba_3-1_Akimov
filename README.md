# Laba_3-1_Akimov
Работа проводилась в google colab
Для начала клонируйте репозиторий гитхаб'а:
```Ruby
!git clone https://github.com/Vokoon/Laba_3-1_Akimov.git
```
Если вы работаете в google colab, то должно вывести следующее
![image](https://github.com/Vokoon/Laba_3-1_Akimov/assets/120046709/ad1c33af-faad-4475-b419-62710aca6738)

Это библиотека необходима для чтения midi файлов, установите её для работы:
```Ruby
!pip install pretty_midi
```
![image](https://github.com/Vokoon/Laba_3-1_Akimov/assets/120046709/ec1d1c8a-4e58-436d-9f96-a10a06a80120)

```Ruby
!pip install tensorflow
```
![image](https://github.com/Vokoon/Laba_3-1_Akimov/assets/120046709/d074ea73-a3ab-479c-9c73-1d16ac646c19)

Теперь вы можете загрузить и обработать MIDI-файлы:
```Ruby

import os
import pretty_midi

midi_folder_path = '/content/midi'

midi_data = []
for file in os.listdir(midi_folder_path):
    if file.endswith('.mid'):
        midi_path = os.path.join(midi_folder_path, file)
        midi = pretty_midi.PrettyMIDI(midi_path)
        # Здесь вы можете добавить код для извлечения нужных вам данных из midi
        midi_data.append(midi)
```
