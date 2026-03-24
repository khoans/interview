**50.** Design a Hospital Management System using mixed patterns. Strategy (treatment workflows) + State (patient flow) + Factory (staff types) + Decorator (billing + insurance).

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Hospital Management System — Mixed Patterns

**Patterns:** Strategy + State + Factory + Decorator
**Why mixed?** Treatment varies by department (Strategy), patients flow through admission stages (State), staff creation varies by role (Factory), billing adds layers of charges/discounts (Decorator).

### Text UML Diagram

```
Factory (Staff)              Strategy (Treatment)
┌──────────────────┐        ┌────────────────────┐
│  StaffFactory    │        │  <<interface>>      │
│──────────────────│        │  TreatmentPlan      │
│ +create(role)    │        │────────────────────│
│  -> Doctor       │        │ +treat(patient)     │
│  -> Nurse        │        │ +getEstimatedCost() │
│  -> Technician   │        │  Emergency, Surgery,│
└──────────────────┘        │  Outpatient, ICU    │
                            └────────────────────┘
State (Patient Flow)
  REGISTERED ──triage──→ TRIAGED ──admit──→ ADMITTED
  ADMITTED ──treat──→ UNDER_TREATMENT ──discharge──→ DISCHARGED
  Any ──(emergency)──→ ADMITTED (fast-track)

Decorator (Billing)
  BaseBill ──→ MedicationDecorator ──→ LabTestDecorator
           ──→ InsuranceDecorator ──→ RoomChargeDecorator
```

### Key Design Decisions

1. **Strategy** for treatment — ER protocol differs from surgery differs from outpatient. Strategy encapsulates department-specific workflows.
2. **State** for patient flow — Enforces hospital process (can't discharge without treatment, can't treat without admission).
3. **Factory** for staff — Different roles have different qualifications, schedules, and responsibilities.
4. **Decorator** for billing — Stack charges (room, meds, labs) and discounts (insurance, gov program) without subclass explosion.

### Java Implementation

```java
import java.util.*;

// ============================================================
// FACTORY — Staff types
// ============================================================

abstract class Staff {
    private final String id;
    private final String name;
    private final String department;

    protected Staff(String id, String name, String department) {
        this.id = id;
        this.name = name;
        this.department = department;
    }

    public String getId() { return id; }
    public String getName() { return name; }
    public String getDepartment() { return department; }
    public abstract String getRole();

    @Override
    public String toString() { return getRole() + " " + name + " (" + department + ")"; }
}

class Doctor extends Staff {
    private final String specialization;
    public Doctor(String id, String name, String dept, String specialization) {
        super(id, name, dept);
        this.specialization = specialization;
    }
    public String getSpecialization() { return specialization; }
    @Override public String getRole() { return "Dr."; }
}

class Nurse extends Staff {
    public Nurse(String id, String name, String dept) { super(id, name, dept); }
    @Override public String getRole() { return "Nurse"; }
}

class Technician extends Staff {
    private final String certification;
    public Technician(String id, String name, String dept, String cert) {
        super(id, name, dept);
        this.certification = cert;
    }
    @Override public String getRole() { return "Tech"; }
}

class StaffFactory {
    public Staff create(String role, String id, String name, String dept, String extra) {
        return switch (role.toLowerCase()) {
            case "doctor" -> new Doctor(id, name, dept, extra);
            case "nurse" -> new Nurse(id, name, dept);
            case "technician" -> new Technician(id, name, dept, extra);
            default -> throw new IllegalArgumentException("Unknown role: " + role);
        };
    }
}

// ============================================================
// PATIENT
// ============================================================

class Patient {
    private final String id;
    private final String name;
    private final int age;
    private final String insuranceId; // null if uninsured
    private final List<String> treatmentLog = new ArrayList<>();

    public Patient(String id, String name, int age, String insuranceId) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.insuranceId = insuranceId;
    }

    public String getId() { return id; }
    public String getName() { return name; }
    public int getAge() { return age; }
    public String getInsuranceId() { return insuranceId; }
    public boolean hasInsurance() { return insuranceId != null; }
    public void addTreatmentNote(String note) { treatmentLog.add(note); }
    public List<String> getTreatmentLog() { return Collections.unmodifiableList(treatmentLog); }
}

// ============================================================
// STRATEGY — Treatment plans by department
// ============================================================

interface TreatmentPlan {
    void treat(Patient patient, Doctor doctor);
    double getEstimatedCost();
    String getDepartment();
}

class EmergencyTreatment implements TreatmentPlan {
    @Override
    public void treat(Patient patient, Doctor doctor) {
        System.out.println("EMERGENCY: " + doctor + " treating " + patient.getName());
        patient.addTreatmentNote("Emergency stabilization by " + doctor.getName());
        patient.addTreatmentNote("Vitals monitored, IV administered");
    }
    @Override public double getEstimatedCost() { return 5000.0; }
    @Override public String getDepartment() { return "Emergency"; }
}

class SurgeryTreatment implements TreatmentPlan {
    private final String procedureName;

    public SurgeryTreatment(String procedureName) { this.procedureName = procedureName; }

    @Override
    public void treat(Patient patient, Doctor doctor) {
        System.out.println("SURGERY: " + procedureName + " by " + doctor);
        patient.addTreatmentNote("Pre-op preparation");
        patient.addTreatmentNote("Surgery: " + procedureName + " by " + doctor.getName());
        patient.addTreatmentNote("Post-op recovery monitoring");
    }
    @Override public double getEstimatedCost() { return 25000.0; }
    @Override public String getDepartment() { return "Surgery"; }
}

class OutpatientTreatment implements TreatmentPlan {
    @Override
    public void treat(Patient patient, Doctor doctor) {
        System.out.println("OUTPATIENT: Consultation with " + doctor);
        patient.addTreatmentNote("Consultation with " + doctor.getName());
        patient.addTreatmentNote("Prescription issued");
    }
    @Override public double getEstimatedCost() { return 300.0; }
    @Override public String getDepartment() { return "Outpatient"; }
}

// ============================================================
// STATE — Patient flow through hospital
// ============================================================

interface PatientState {
    void triage(PatientRecord record, int severity);
    void admit(PatientRecord record, TreatmentPlan plan);
    void treat(PatientRecord record, Doctor doctor);
    void discharge(PatientRecord record);
    String getStateName();
}

class PatientRecord {
    private final Patient patient;
    private PatientState state;
    private TreatmentPlan treatmentPlan;
    private int triageSeverity; // 1=critical, 5=minor
    private final List<String> stateHistory = new ArrayList<>();

    public PatientRecord(Patient patient) {
        this.patient = patient;
        this.state = new RegisteredState();
        stateHistory.add("REGISTERED");
    }

    public void triage(int severity) { state.triage(this, severity); }
    public void admit(TreatmentPlan plan) { state.admit(this, plan); }
    public void treat(Doctor doctor) { state.treat(this, doctor); }
    public void discharge() { state.discharge(this); }

    public Patient getPatient() { return patient; }
    public void setState(PatientState state) {
        this.state = state;
        stateHistory.add(state.getStateName());
        System.out.printf("[%s] %s -> %s%n", patient.getName(), stateHistory.get(stateHistory.size() - 2), state.getStateName());
    }
    public TreatmentPlan getTreatmentPlan() { return treatmentPlan; }
    public void setTreatmentPlan(TreatmentPlan plan) { this.treatmentPlan = plan; }
    public void setTriageSeverity(int severity) { this.triageSeverity = severity; }
    public String getStateName() { return state.getStateName(); }
}

class RegisteredState implements PatientState {
    @Override
    public void triage(PatientRecord record, int severity) {
        record.setTriageSeverity(severity);
        record.setState(new TriagedState());
    }
    @Override public void admit(PatientRecord r, TreatmentPlan p) { System.out.println("Must triage first"); }
    @Override public void treat(PatientRecord r, Doctor d) { System.out.println("Must admit first"); }
    @Override public void discharge(PatientRecord r) { System.out.println("Patient not admitted"); }
    @Override public String getStateName() { return "REGISTERED"; }
}

class TriagedState implements PatientState {
    @Override public void triage(PatientRecord r, int s) { System.out.println("Already triaged"); }
    @Override
    public void admit(PatientRecord record, TreatmentPlan plan) {
        record.setTreatmentPlan(plan);
        record.setState(new AdmittedState());
    }
    @Override public void treat(PatientRecord r, Doctor d) { System.out.println("Must admit first"); }
    @Override public void discharge(PatientRecord r) { System.out.println("Not admitted yet"); }
    @Override public String getStateName() { return "TRIAGED"; }
}

class AdmittedState implements PatientState {
    @Override public void triage(PatientRecord r, int s) { System.out.println("Already triaged"); }
    @Override public void admit(PatientRecord r, TreatmentPlan p) { System.out.println("Already admitted"); }
    @Override
    public void treat(PatientRecord record, Doctor doctor) {
        record.getTreatmentPlan().treat(record.getPatient(), doctor);
        record.setState(new UnderTreatmentState());
    }
    @Override public void discharge(PatientRecord r) { System.out.println("Must treat before discharge"); }
    @Override public String getStateName() { return "ADMITTED"; }
}

class UnderTreatmentState implements PatientState {
    @Override public void triage(PatientRecord r, int s) { System.out.println("Under treatment"); }
    @Override public void admit(PatientRecord r, TreatmentPlan p) { System.out.println("Already admitted"); }
    @Override public void treat(PatientRecord r, Doctor d) { System.out.println("Already treating"); }
    @Override
    public void discharge(PatientRecord record) {
        System.out.println("Discharging: " + record.getPatient().getName());
        record.setState(new DischargedState());
    }
    @Override public String getStateName() { return "UNDER_TREATMENT"; }
}

class DischargedState implements PatientState {
    @Override public void triage(PatientRecord r, int s) { done(); }
    @Override public void admit(PatientRecord r, TreatmentPlan p) { done(); }
    @Override public void treat(PatientRecord r, Doctor d) { done(); }
    @Override public void discharge(PatientRecord r) { done(); }
    @Override public String getStateName() { return "DISCHARGED"; }
    private void done() { System.out.println("Patient already discharged"); }
}

// ============================================================
// DECORATOR — Billing with stackable charges
// ============================================================

interface Bill {
    double getTotal();
    String getItemizedBreakdown();
}

class BaseBill implements Bill {
    private final double treatmentCost;
    public BaseBill(double treatmentCost) { this.treatmentCost = treatmentCost; }

    @Override public double getTotal() { return treatmentCost; }
    @Override public String getItemizedBreakdown() {
        return String.format("Treatment: $%.2f", treatmentCost);
    }
}

abstract class BillDecorator implements Bill {
    protected final Bill wrapped;
    protected BillDecorator(Bill wrapped) { this.wrapped = wrapped; }
}

class RoomChargeDecorator extends BillDecorator {
    private final int nights;
    private final double perNightRate;

    public RoomChargeDecorator(Bill bill, int nights, double perNightRate) {
        super(bill);
        this.nights = nights;
        this.perNightRate = perNightRate;
    }

    @Override public double getTotal() { return wrapped.getTotal() + (nights * perNightRate); }
    @Override public String getItemizedBreakdown() {
        return wrapped.getItemizedBreakdown()
                + String.format("%nRoom (%d nights x $%.0f): $%.2f", nights, perNightRate, nights * perNightRate);
    }
}

class MedicationDecorator extends BillDecorator {
    private final double medicationCost;
    public MedicationDecorator(Bill bill, double cost) { super(bill); this.medicationCost = cost; }

    @Override public double getTotal() { return wrapped.getTotal() + medicationCost; }
    @Override public String getItemizedBreakdown() {
        return wrapped.getItemizedBreakdown()
                + String.format("%nMedication: $%.2f", medicationCost);
    }
}

class LabTestDecorator extends BillDecorator {
    private final String testName;
    private final double cost;

    public LabTestDecorator(Bill bill, String testName, double cost) {
        super(bill);
        this.testName = testName;
        this.cost = cost;
    }

    @Override public double getTotal() { return wrapped.getTotal() + cost; }
    @Override public String getItemizedBreakdown() {
        return wrapped.getItemizedBreakdown()
                + String.format("%nLab Test (%s): $%.2f", testName, cost);
    }
}

class InsuranceDecorator extends BillDecorator {
    private final double coveragePercent;
    private final String provider;

    public InsuranceDecorator(Bill bill, String provider, double coveragePercent) {
        super(bill);
        this.provider = provider;
        this.coveragePercent = coveragePercent;
    }

    @Override
    public double getTotal() {
        return wrapped.getTotal() * (1 - coveragePercent / 100.0);
    }

    @Override
    public String getItemizedBreakdown() {
        double discount = wrapped.getTotal() * (coveragePercent / 100.0);
        return wrapped.getItemizedBreakdown()
                + String.format("%nInsurance (%s, %.0f%%): -$%.2f", provider, coveragePercent, discount);
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class HospitalDemo {
    public static void main(String[] args) {
        StaffFactory staffFactory = new StaffFactory();
        Doctor surgeon = (Doctor) staffFactory.create("doctor", "D001", "Dr. Smith", "Surgery", "Orthopedics");
        Doctor erDoctor = (Doctor) staffFactory.create("doctor", "D002", "Dr. Lee", "Emergency", "Trauma");

        // Patient 1: Surgery with insurance
        System.out.println("=== Patient 1: Surgery ===");
        Patient p1 = new Patient("P001", "Alice Johnson", 45, "INS-12345");
        PatientRecord record1 = new PatientRecord(p1);

        record1.triage(2);                                    // Registered -> Triaged
        record1.admit(new SurgeryTreatment("Knee Replacement")); // Triaged -> Admitted
        record1.treat(surgeon);                                // Admitted -> Under Treatment
        record1.discharge();                                   // Under Treatment -> Discharged

        // Build bill with decorators
        Bill bill1 = new BaseBill(25000.0);
        bill1 = new RoomChargeDecorator(bill1, 3, 500.0);
        bill1 = new MedicationDecorator(bill1, 1200.0);
        bill1 = new LabTestDecorator(bill1, "Blood Panel", 350.0);
        bill1 = new InsuranceDecorator(bill1, "BlueCross", 80.0); // 80% coverage

        System.out.println("\n--- Bill ---");
        System.out.println(bill1.getItemizedBreakdown());
        System.out.printf("TOTAL DUE: $%.2f%n", bill1.getTotal());

        // Patient 2: ER visit, no insurance
        System.out.println("\n=== Patient 2: Emergency ===");
        Patient p2 = new Patient("P002", "Bob Miller", 30, null);
        PatientRecord record2 = new PatientRecord(p2);

        record2.triage(1); // critical
        record2.admit(new EmergencyTreatment());
        record2.treat(erDoctor);
        record2.discharge();

        Bill bill2 = new BaseBill(5000.0);
        bill2 = new MedicationDecorator(bill2, 800.0);
        bill2 = new LabTestDecorator(bill2, "CT Scan", 2500.0);
        // No insurance decorator — full cost

        System.out.println("\n--- Bill (uninsured) ---");
        System.out.println(bill2.getItemizedBreakdown());
        System.out.printf("TOTAL DUE: $%.2f%n", bill2.getTotal());
    }
}
```

### Interview Talking Points

- **Decorator for billing is perfect:** Hospital bills are inherently stackable — base treatment + room + meds + labs + insurance discount. Each layer wraps the previous. No need for 20 `Bill` subclasses for every combination.
- **State enforces medical protocol:** Can't discharge without treatment, can't treat without admission. State pattern codifies hospital procedures as compile-time rules.
- **Real-world:** Epic (hospital ERP) uses state machines for patient encounters. Insurance processing uses decorator-like layered calculations (deductible, copay, coverage cap). HL7/FHIR standards define patient state transitions.
- **Strategy for treatment:** ER protocol (stabilize, vitals, IV) is completely different from surgery (pre-op, operation, post-op). Same `treat()` call, different execution.
