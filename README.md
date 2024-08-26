stateDiagram-v2
    [*] --> NOINFO

    NOINFO --> DISPONIBLE : Datos iniciales
    
    DISPONIBLE --> RESERVADO : Reserva realizada
    DISPONIBLE --> OCUPADO : Ocupación directa
    DISPONIBLE --> MANTENIMIENTO : Necesita mantenimiento

    RESERVADO --> OCUPADO : Cliente se registra
    RESERVADO --> CANCELADO : Reserva cancelada

    OCUPADO --> LIMPIEZA : Cliente se retira

    LIMPIEZA --> DISPONIBLE : Limpieza completada

    MANTENIMIENTO --> LIMPIEZA : Mantenimiento finalizado

    DISPONIBLE --> DESHABILITADO : Problemas en la habitación

    DESHABILITADO --> DISPONIBLE : Problemas solucionados

    state NOINFO {
        [*] --> NOINFO
    }

    state DISPONIBLE {
        [*] --> DISPONIBLE
    }

    state RESERVADO {
        [*] --> RESERVADO
    }

    state OCUPADO {
        [*] --> OCUPADO
    }

    state LIMPIEZA {
        [*] --> LIMPIEZA
    }

    state MANTENIMIENTO {
        [*] --> MANTENIMIENTO
    }

    state DESHABILITADO {
        [*] --> DESHABILITADO
    }

    [*] --> NOINFO



---------------------------------------------------------------------
import java.util.Scanner;

public class Room {
    // Definición de la habitación
    private static final String DEFINICION = "Una parte o división de un edificio cerrada por paredes, suelo y techo";
    private String roomName;
    private int currentStatus;
    private int currentEndline;
    private int nextStatus;
    private int nextEndline;
    private int afterStatus;
    private int afterEndline;

    private final String[] estados = {"NOINFO", "DISPONIBLE", "RESERVADO", "OCUPADO", "LIMPIEZA", "MANTENIMIENTO", "DESHABILITADO"};

    public Room() {
        this(new String[]{"NOINFO", "0", "0", "0", "0", "0", "0"});
    }

    public Room(String[] roomData) {
        this.roomName = roomData[0];
        this.currentStatus = Integer.parseInt(roomData[1]);
        this.currentEndline = Integer.parseInt(roomData[2]);
        this.nextStatus = Integer.parseInt(roomData[3]);
        this.nextEndline = Integer.parseInt(roomData[4]);
        this.afterStatus = Integer.parseInt(roomData[5]);
        this.afterEndline = Integer.parseInt(roomData[6]);
    }

    public String getEstadoActual() {
        return roomName + ": \t " + estados[currentStatus] + ".";
    }

    public String getEstadoCompleto() {
        return roomName + ":    " + estados[currentStatus] + "    " + currentEndline + " días restantes  -->  " 
               + estados[nextStatus] + "    " + nextEndline + " días siguientes  -->  " 
               + estados[afterStatus] + "    " + afterEndline + " días después.";
    }

    public String protocoloAmanecer() {
        String actualizacionEstado = roomName + ": no se encontró información, por favor revise.";
        
        if (currentEndline > 0) {
            actualizacionEstado = roomName + " está " + estados[currentStatus] + ", " + currentEndline + " días restantes.";
        } else if (currentEndline == 0 && nextEndline > 0) {
            actualizacionEstado = roomName + " pasó de " + estados[currentStatus] + " a " + estados[nextStatus] + ".";
            currentStatus = nextStatus;
            currentEndline = nextEndline;
            nextStatus = afterStatus;
            nextEndline = afterEndline;
            afterStatus = 0;
            afterEndline = 0;
        } else if (currentEndline == 0 && nextEndline == 0) {
            Scanner sc = new Scanner(System.in);
            System.out.println(roomName + " terminó " + estados[currentStatus] + ", se necesita una actualización:");
            System.out.println("1. DISPONIBLE\n2. RESERVADO\n3. OCUPADO\n4. LIMPIEZA\n5. MANTENIMIENTO\n6. DESHABILITADO");
            int avisoActualizacion = sc.nextInt();

            if (avisoActualizacion == 1) {
                currentStatus = 1;
                currentEndline = 1;
                actualizacionEstado = roomName + " ahora está " + estados[currentStatus] + ".";
            } else if (avisoActualizacion == 2) {
                System.out.println(estados[2] + ": Ingrese la cantidad de días:");
                int diasReservado = sc.nextInt();
                currentStatus = 2;
                currentEndline = diasReservado;
                System.out.println(estados[3] + ": Ingrese la cantidad de días:");
                int diasOcupado = sc.nextInt();
                nextStatus = 3;
                nextEndline = diasOcupado;
                actualizacionEstado = roomName + " ahora está " + estados[currentStatus] + ".";
            } else if (avisoActualizacion == 3) {
                System.out.println(estados[3] + ": Ingrese la cantidad de días:");
                int diasOcupado = sc.nextInt();
                currentStatus = 3;
                currentEndline = diasOcupado;
                nextStatus = 4;
                nextEndline = 1;
                actualizacionEstado = roomName + " ahora está " + estados[currentStatus] + ".";
            } else if (avisoActualizacion == 4) {
                currentStatus = 4;
                currentEndline = 1;
                nextStatus = 1;
                nextEndline = 1;
                actualizacionEstado = roomName + " ahora está " + estados[currentStatus] + ".";
            } else if (avisoActualizacion == 5) {
                System.out.println(estados[5] + ": Ingrese la cantidad de días:");
                int diasMantenimiento = sc.nextInt();
                currentStatus = 5;
                currentEndline = diasMantenimiento;
                nextStatus = 4;
                nextEndline = 1;
                actualizacionEstado = roomName + " ahora está " + estados[currentStatus] + ".";
            } else if (avisoActualizacion == 6) {
                System.out.println(estados[6] + ": Ingrese la cantidad de días:");
                int diasDeshabilitado = sc.nextInt();
                currentStatus = 6;
                currentEndline = diasDeshabilitado;
                System.out.println(roomName + " está " + estados[currentStatus] + ", se necesita una actualización:");
                System.out.println("Ingrese el estado siguiente y la cantidad de días:");
                nextStatus = sc.nextInt();
                nextEndline = sc.nextInt();
                actualizacionEstado = roomName + " está " + estados[currentStatus] + ".";
            }
        }
        return actualizacionEstado;
    }
}
