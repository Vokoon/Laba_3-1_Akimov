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
Создаём модель RNN(LSTM)
```Ruby
import os
import pretty_midi
from music21 import converter, note, chord, instrument

def convert_midi_files_in_directory(directory):
    notes = []
    for file in os.listdir(directory):
        if file.endswith(".mid"):
            midi_path = os.path.join(directory, file)
            try:
                midi = converter.parse(midi_path)
                notes_to_parse = None

                try: 
                    s2 = instrument.partitionByInstrument(midi)
                    if s2:  
                        notes_to_parse = s2.parts[0].recurse() 
                    else:
                        notes_to_parse = midi.flat.notes
                except Exception as e:  
                    print(f"Ошибка при разборе дорожек: {e}")
                    notes_to_parse = midi.flat.notes

                for element in notes_to_parse:
                    if isinstance(element, note.Note):
                        notes.append(str(element.pitch))
                    elif isinstance(element, chord.Chord):
                        notes.append('.'.join(str(n) for n in element.normalOrder))
                print(f"Файл {file} успешно обработан.")
            except Exception as e:
                print(f"Не удалось обработать файл {file}: {e}")
    return notes

directory = '/content/Laba_3-1_Akimov/midi'
try:
    all_notes = convert_midi_files_in_directory(directory)
    note_to_int = {note: number for number, note in enumerate(sorted(set(all_notes)))}
    int_to_note = {number: note for note, number in note_to_int.items()}
    numeric_notes = [note_to_int[note] for note in all_notes]
    print("Конвертация завершена успешно.")
except Exception as e:
    print(f"Произошла ошибка при конвертации: {e}")
```
Обучаем модель RNN(LSTM)
Процесс может быть очень долгим, кол-во "эпох" меняйте по своему желанию, насколько качественный продукт вы хотите получить, я ограничусь 3.
В конце компиляции может выйти ошибка, перезапускать код нет необходимости, можем продолжать работу.

```Rudy
from keras.callbacks import ModelCheckpoint

split = int(n_patterns * 0.9)  # 90% для обучения, 10% для валидации
train_input = network_input[:split]
train_output = network_output[:split]
validation_input = network_input[split:]
validation_output = network_output[split:]

filepath = "weights-improvement-{epoch:02d}-{loss:.4f}-bigger.hdf5"
checkpoint = ModelCheckpoint(
    filepath, monitor='loss', verbose=1, save_best_only=True, mode='min'
)

callbacks_list = [checkpoint]

history = model.fit(train_input, train_output, epochs=3, batch_size=64,
                    validation_data=(validation_input, validation_output),
                    callbacks=callbacks_list, verbose=1)

import matplotlib.pyplot as plt

plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Потери модели')
plt.ylabel('Потери')
plt.xlabel('Эпоха')
plt.legend(['Обучение', 'Валидация'], loc='upper left')
plt.show()

plt.plot(history.history['accuracy'])
plt.plot(history.history['val_accuracy'])
plt.title('Точность модели')
plt.ylabel('Точность')
plt.xlabel('Эпоха')
plt.legend(['Обучение', 'Валидация'], loc='upper left')
plt.show()
```
