#include <lpc21xx.h>   // Header for LPC2129

#define RED     (1 << 10)
#define YELLOW  (1 << 11)
#define GREEN   (1 << 12)
#define BUZZER  (1 << 15)
#define RFID    (1 << 16)   // Input pin

void delay(unsigned int time) {
    unsigned int i, j;
    for(i = 0; i < time; i++)
        for(j = 0; j < 6000; j++);
}

void traffic_normal() {
    // RED ON
    IOSET0 = RED;
    IOCLR0 = YELLOW | GREEN;
    delay(1000);

    // YELLOW ON
    IOSET0 = YELLOW;
    IOCLR0 = RED | GREEN;
    delay(500);

    // GREEN ON
    IOSET0 = GREEN;
    IOCLR0 = RED | YELLOW;
    delay(1000);
}

void ambulance_mode() {
    // GREEN ON + BUZZER ON
    IOSET0 = GREEN | BUZZER;
    IOCLR0 = RED | YELLOW;
    delay(2000);

    // BUZZER OFF
    IOCLR0 = BUZZER;
}

int main(void) {
    IODIR0 = RED | YELLOW | GREEN | BUZZER; // Set pins as output
    IODIR0 &= ~RFID; // Set RFID pin as input

    while(1) {
        if (!(IOPIN0 & RFID)) {  // If RFID is LOW (pressed)
            ambulance_mode();
        } else {
            traffic_normal();
        }
    }
}
