// Import library yang dibutuhkan
import java.util.ArrayList; // Untuk menyimpan daftar antrian pasien
import java.util.Scanner;   // Untuk membaca input dari pengguna

public class HospitalQueueSystemQuestion {

    // Daftar pasien dalam bentuk antrian (prioritas)
    private static ArrayList<Patient> patientQueue = new ArrayList<>();
    private static Scanner scanner = new Scanner(System.in); // Scanner untuk input dari user

    public static void main(String[] args) {
        boolean running = true;

        System.out.println("Welcome to Hospital Queue Management System");

        // Perulangan utama menu
        while (running) {
            displayMenu(); // Tampilkan menu utama
            int choice = getValidIntInput("Enter your choice: "); // Ambil input pilihan

            // Jalankan fungsi sesuai pilihan
            switch (choice) {
                case 1: addPatient(); break;
                case 2: serveNextPatient(); break;
                case 3: displayQueue(); break;
                case 4: updatePriority(); break;
                case 5: searchPatient(); break;
                case 6:
                    System.out.println("Thank you for using Hospital Queue Management System. Goodbye!");
                    running = false;
                    break;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        }

        scanner.close(); // Tutup scanner
    }

    // Fungsi menampilkan menu
    private static void displayMenu() {
        System.out.println("\n===== HOSPITAL QUEUE SYSTEM =====");
        System.out.println("1. Add a new patient to the queue");
        System.out.println("2. Serve next patient");
        System.out.println("3. Display current queue");
        System.out.println("4. Update patient priority");
        System.out.println("5. Search for a patient");
        System.out.println("6. Exit");
        System.out.println("=================================");
    }

    // Fungsi untuk menambahkan pasien baru
    private static void addPatient() {
        System.out.println("\n--- Add New Patient ---");
        String name = getValidStringInput("Enter patient's name: ");
        int age = getValidIntInput("Enter patient's age: ");
        String condition = getValidStringInput("Enter medical condition: ");
        int priority = getValidIntInRange("Enter priority (1-Critical to 5-Non-urgent): ", 1, 5);

        // Buat objek pasien dan tambahkan ke antrian
        Patient newPatient = new Patient(name, age, condition, priority);
        patientQueue.add(newPatient);

        // Urutkan antrian berdasarkan prioritas
        patientQueue.sort((p1, p2) -> Integer.compare(p1.getPriority(), p2.getPriority()));

        System.out.println("Patient added successfully.");
    }

    // Fungsi untuk melayani pasien selanjutnya
    private static void serveNextPatient() {
        System.out.println("\n--- Serve Next Patient ---");
        if (patientQueue.isEmpty()) {
            System.out.println("No patients in queue.");
        } else {
            Patient next = patientQueue.remove(0); // Ambil dan hapus pasien pertama
            System.out.println("Serving Patient: " + next.getName() +
                " (Age: " + next.getAge() + ", Condition: " + next.getCondition() +
                ", Priority: " + getPriorityText(next.getPriority()) + ")");
        }
    }

    // Fungsi menampilkan seluruh antrian
    private static void displayQueue() {
        System.out.println("\n--- Current Queue ---");
        if (patientQueue.isEmpty()) {
            System.out.println("No patients in the queue.");
        } else {
            int index = 1;
            for (Patient p : patientQueue) {
                System.out.println(index++ + ". " + p.getName() +
                    " (Age: " + p.getAge() + ", Condition: " + p.getCondition() +
                    ", Priority: " + getPriorityText(p.getPriority()) + ")");
            }
        }
    }

    // Fungsi memperbarui prioritas pasien berdasarkan nama
    private static void updatePriority() {
        System.out.println("\n--- Update Patient Priority ---");
        String name = getValidStringInput("Enter patient's name to update: ");
        boolean found = false;

        for (Patient p : patientQueue) {
            if (p.getName().equalsIgnoreCase(name)) {
                int newPriority = getValidIntInRange("Enter new priority (1-Critical to 5-Non-urgent): ", 1, 5);
                p.setPriority(newPriority);
                found = true;

                // Urutkan kembali antrian
                patientQueue.sort((p1, p2) -> Integer.compare(p1.getPriority(), p2.getPriority()));
                System.out.println("Priority updated successfully.");
                break;
            }
        }

        if (!found) {
            System.out.println("Patient not found.");
        }
    }

    // Fungsi mencari pasien berdasarkan nama
    private static void searchPatient() {
        System.out.println("\n--- Search for Patient ---");
        String name = getValidStringInput("Enter patient's name to search: ");
        boolean found = false;

        for (Patient p : patientQueue) {
            if (p.getName().equalsIgnoreCase(name)) {
                System.out.println("Found Patient: " + p.getName() +
                    " (Age: " + p.getAge() + ", Condition: " + p.getCondition() +
                    ", Priority: " + getPriorityText(p.getPriority()) + ")");
                found = true;
                break;
            }
        }

        if (!found) {
            System.out.println("Patient not found.");
        }
    }

    // Fungsi untuk mengubah angka prioritas menjadi teks
    private static String getPriorityText(int priority) {
        switch (priority) {
            case 1: return "1-Critical";
            case 2: return "2-Urgent";
            case 3: return "3-High";
            case 4: return "4-Medium";
            case 5: return "5-Non-urgent";
            default: return "Unknown";
        }
    }

    // Fungsi untuk mengambil input angka valid
    private static int getValidIntInput(String prompt) {
        int value;
        while (true) {
            System.out.print(prompt);
            try {
                value = Integer.parseInt(scanner.nextLine().trim());
                break;
            } catch (NumberFormatException e) {
                System.out.println("Invalid input. Please enter a number.");
            }
        }
        return value;
    }

    // Fungsi mengambil angka dengan batas bawah dan atas
    private static int getValidIntInRange(String prompt, int min, int max) {
        int value;
        while (true) {
            value = getValidIntInput(prompt);
            if (value >= min && value <= max) {
                break;
            }
            System.out.println("Please enter a value between " + min + " and " + max + ".");
        }
        return value;
    }

    // Fungsi mengambil input string (tidak boleh kosong)
    private static String getValidStringInput(String prompt) {
        String value;
        while (true) {
            System.out.print(prompt);
            value = scanner.nextLine().trim();
            if (!value.isEmpty()) {
                break;
            }
            System.out.println("Input cannot be empty. Please try again.");
        }
        return value;
    }
}

// Kelas pasien untuk menyimpan data pasien
class Patient {
    private String name;
    private int age;
    private String condition;
    private int priority;

    // Constructor
    public Patient(String name, int age, String condition, int priority) {
        this.name = name;
        this.age = age;
        this.condition = condition;
        this.priority = priority;
    }

    // Getter dan Setter
    public String getName() { return name; }
    public int getAge() { return age; }
    public String getCondition() { return condition; }
    public int getPriority() { return priority; }
    public void setPriority(int priority) { this.priority = priority; }
}
