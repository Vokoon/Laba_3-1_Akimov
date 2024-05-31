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
from music21 import converter, note, chord, instrument

# Функция для чтения всех MIDI файлов в указанной директории
def convert_midi_files_in_directory(directory):
    notes = []
    for file in os.listdir(directory):
        if file.endswith(".mid"):
            midi_path = os.path.join(directory, file)
            try:
                midi = converter.parse(midi_path)
                notes_to_parse = None

                try:  # файл содержит инструментальные дорожки
                    s2 = instrument.partitionByInstrument(midi)
                    if s2:  # проверка на наличие инструментальных дорожек
                        notes_to_parse = s2.parts[0].recurse() 
                    else:
                        notes_to_parse = midi.flat.notes
                except Exception as e:  # файл содержит ноты в одной дорожке
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

# Пример использования функции
directory = '/content/Laba_3-1_Akimov/midi'
try:
    all_notes = convert_midi_files_in_directory(directory)
    note_to_int = {note: number for number, note in enumerate(sorted(set(all_notes)))}
    numeric_notes = [note_to_int[note] for note in all_notes]
    print("Конвертация завершена успешно.")
except Exception as e:
    print(f"Произошла ошибка при конвертации: {e}")
```
Создаём модель RNN(LSTM)
```Rudy
from keras.callbacks import ModelCheckpoint

# Разделение данных на обучающую и валидационную выборки
split = int(n_patterns * 0.9)  # 90% для обучения, 10% для валидации
train_input = network_input[:split]
train_output = network_output[:split]
validation_input = network_input[split:]
validation_output = network_output[split:]

# Создание чекпоинта для сохранения лучшей модели
filepath = "weights-improvement-{epoch:02d}-{loss:.4f}-bigger.hdf5"
checkpoint = ModelCheckpoint(
    filepath, monitor='loss', verbose=1, save_best_only=True, mode='min'
)

callbacks_list = [checkpoint]

# Обучение модели
history = model.fit(train_input, train_output, epochs=10, batch_size=64,
                    validation_data=(validation_input, validation_output),
                    callbacks=callbacks_list, verbose=1)

# Вывод истории обучения
import matplotlib.pyplot as plt

# График потерь на обучающей и валидационной выборке
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.title('Потери модели')
plt.ylabel('Потери')
plt.xlabel('Эпоха')
plt.legend(['Обучение', 'Валидация'], loc='upper left')
plt.show()

# График точности на обучающей и валидационной выборке
plt.plot(history.history['accuracy'])
plt.plot(history.history['val_accuracy'])
plt.title('Точность модели')
plt.ylabel('Точность')
plt.xlabel('Эпоха')
plt.legend(['Обучение', 'Валидация'], loc='upper left')
plt.show()
```
