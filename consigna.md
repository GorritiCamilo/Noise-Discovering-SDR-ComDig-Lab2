# Laboratorio Numero 3, Comunicacines Digitales

El siguiente código genera una señal (hipótesis) binaria compleja que permite modificar, a través de la variable "ref", la probabilidad de ocurrencia de los valores binarios: 

```py
z = np.random.uniform(size=nb_samples)
x = [1 if z[1]>ref else 0 for i in range (len(z) ) ]

txSignal = (2*np.repeat(x ,2**4)-1) + 1j*(2*np.repeat(x , 2**4)-1)
```

Inicialmente configure el valor de la variable "ref" a un valor de 0.5 .

**1.-** Realizar  un gráfica temporal  y el histograma de cada una de las componentes de la señal. Realizar también una gráfica en frecuencia. Describa con sus propias palabras lo que observa de las gráficas.

Considerando la siguiente configuración del SDR:

```py
Uri                = "ip:192.168.1.33"  # Dirección IP del SDR
Loopback           = 0                  # Sin lazo de retorno (0=desactivado)
samplingRate       = int(2e6)           # Frecuencia de muestreo: 2 MSPS
#Tx:
TxLo               = int(350e6)         # Frecuencia portadora Tx: 350 MHz 
TxAtten            = -79                # Atenuación del transmisor [dB]
TxRfBw             = int(2e6)           # Ancho de banda RF del transmisor [Hz]
Tx_cyclic_buffer   = True               # Sin buffer cíclico de Tx (no transmitimos)
#Rx:
RxLo               = int(2000e6)        # Frecuencia portadora Rx: 2 GHz 
GainControlModes   = "manual"           # Control de ganancia manual
RxHardwareGain     = 70                 # Ganancia del receptor [dB] (máxima sensibilidad)
RxRfBw             = int(2e6)              # Ancho de banda RF del receptor [Hz]
RxBufferSize       = 2**20              # Tamaño del buffer de recepción (≈1M muestras)
```

**2.-** Realice una transmisión y recepción de la señal usando el SDR a través del medio inalámbrico. Normalice la señal recibida de forma tal que tenga potencia unitaria y realice una gráfica temporal y su correspondiente histograma tanto de la componente en fase como en cuadratura. Describa con sus propias palabras lo que observa de las gráficas. Tenga presente de realizar unas cuantas recepciones antes de obtener la recepción deseada cuando se usa el modo transmisión continua ("tx_cyclic_buffer = True"). Esto se hace para evitar capturar la señal en algún estado transitorio inicial y para eliminar del buffer del receptor muestras de señales tomadas con anterioridad. El siguiente código muestra el procedimiento descripto:  

```py
# ---- Transmitter
sdr.tx(txSignal*(2**14))

# Clear Rx buffer
for i in range (0, 10):
    raw_data = sdr.rx()

# ---- Receiver
rxSignal = sdr.rx()
```

**3.-** Filtre la señal con un pulso rectangular de 2**4 muestras distintas de cero y realice una gráfica temporal y su correspondiente histograma tanto de la componente en fase como en cuadratura. 

**4.-** Repita los pasos anteriores pero ahora aumentando la potencia de la señal transmitida, esto es llevando el valor de la variable "TxAtten" al valor -70. Describa con sus propias palabras lo que observa en el histograma de la señal comparándola con lo anteriormente obtenido.

**5.-** Configure el valor de la variable "ref" a un valor de 0.3 y repita los pasos anteriores. Que puede concluir del histograma obtenido de la señal recibida?. 

**6.-** Modifique el código para enviar una hipotesis H∈{−1,−0.3333,+0.3333,+1} con igual probabilidades. Muestre el histograma de la señal recibida por el SDR.  Describa con sus propias palabras lo que observa en la gráfica.

Tenga presente que si la cantidad de muestras de la señal a transmitir ha cambiado, debe eliminar el buffer Y volverlo a configurar para evitar errores de configuración. Las siguiente lineas de código muestra el procedimiento descripto:

```py
sdr.tx_destroy_buffer()
sdr.tx_cyclic_buffer = tx_cyclic_buffer
# ---- Transmitter
sdr.tx(txSignal*(2**14))
```
