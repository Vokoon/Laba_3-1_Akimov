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
from music21 import converter, note, chord

# Функция для чтения всех MIDI файлов в указанной директории
def convert_midi_files_in_directory(directory):
    notes = []
    for file in os.listdir(directory):
        if file.endswith(".mid"):
            midi_path = os.path.join(directory, file)
            midi = converter.parse(midi_path)
            notes_to_parse = None

            try:  # файл содержит инструментальные дорожки
                s2 = instrument.partitionByInstrument(midi)
                notes_to_parse = s2.parts[0].recurse() 
            except:  # файл содержит ноты в одной дорожке
                notes_to_parse = midi.flat.notes

            for element in notes_to_parse:
                if isinstance(element, note.Note):
                    notes.append(str(element.pitch))
                elif isinstance(element, chord.Chord):
                    notes.append('.'.join(str(n) for n in element.normalOrder))
    return notes

directory = '/content/midi/midi'
all_notes = convert_midi_files_in_directory(directory)
```
