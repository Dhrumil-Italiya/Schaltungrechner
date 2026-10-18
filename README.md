#include <iostream>
#include <iomanip>
#include <string>
#include <vector>

double calcCurrent(double volts, double ohms)     { return volts / ohms; }
double calcVoltage(double amps, double ohms)      { return amps * ohms; }
double calcResistance(double volts, double amps)  { return volts / amps; }
double calcPower(double volts, double amps)       { return volts * amps; }

double seriesTotal(const std::vector<double>& r) {
    double total = 0;
    for (double x : r) {
        total += x;
    }
    return total;
}

double parallelTotal(const std::vector<double>& r) {
    double sumOfInverses = 0;
    for (double x : r) {
        sumOfInverses += 1.0 / x;
    }
    return 1.0 / sumOfInverses;
}

double dividerOutput(double vin, double r1, double r2) {
    return vin * r2 / (r1 + r2);
}

double ask(const std::string& text) {
    double value;
    std::cout << text;
    std::cin >> value;
    return value;
}

std::vector<double> readResistors() {
    int n;
    std::cout << "How many resistors? ";
    std::cin >> n;

    std::vector<double> values;
    for (int i = 0; i < n; ++i) {
        values.push_back(ask("R" + std::to_string(i + 1) + " (ohm): "));
    }
    return values;
}

bool allPositive(const std::vector<double>& r) {
    if (r.empty()) {
        return false;
    }
    for (double x : r) {
        if (x <= 0) {
            return false;
        }
    }
    return true;
}

int main() {
    std::cout << std::fixed << std::setprecision(3);
    char again;

    do {
        std::cout << "\n=== Circuit Calculator ===\n"
                  << "1) Current      I = V / R\n"
                  << "2) Voltage      V = I * R\n"
                  << "3) Resistance   R = V / I\n"
                  << "4) Power        P = V * I\n"
                  << "5) Resistors in series\n"
                  << "6) Resistors in parallel\n"
                  << "7) Voltage divider\n"
                  << "Choose: ";
        int choice;
        std::cin >> choice;

        switch (choice) {
            case 1: {
                double v = ask("Voltage (V): ");
                double r = ask("Resistance (ohm): ");
                if (r == 0) {
                    std::cout << "Resistance can't be zero!\n";
                } else {
                    std::cout << "Current = " << calcCurrent(v, r) << " A\n";
                }
                break;
            }
            case 2: {
                double i = ask("Current (A): ");
                double r = ask("Resistance (ohm): ");
                std::cout << "Voltage = " << calcVoltage(i, r) << " V\n";
                break;
            }
            case 3: {
                double v = ask("Voltage (V): ");
                double i = ask("Current (A): ");
                if (i == 0) {
                    std::cout << "Current can't be zero!\n";
                } else {
                    std::cout << "Resistance = " << calcResistance(v, i) << " ohm\n";
                }
                break;
            }
            case 4: {
                double v = ask("Voltage (V): ");
                double i = ask("Current (A): ");
                std::cout << "Power = " << calcPower(v, i) << " W\n";
                break;
            }
            case 5: {
                std::vector<double> r = readResistors();
                if (!allPositive(r)) {
                    std::cout << "Need at least one resistor, all above 0 ohm.\n";
                } else {
                    std::cout << "Series total = " << seriesTotal(r) << " ohm\n";
                }
                break;
            }
            case 6: {
                std::vector<double> r = readResistors();
                if (!allPositive(r)) {
                    std::cout << "Need at least one resistor, all above 0 ohm.\n";
                } else {
                    std::cout << "Parallel total = " << parallelTotal(r) << " ohm\n";
                }
                break;
            }
            case 7: {
                double vin = ask("Input voltage (V): ");
                double r1 = ask("R1 (ohm): ");
                double r2 = ask("R2 (ohm): ");
                if (r1 + r2 == 0) {
                    std::cout << "R1 + R2 can't be zero!\n";
                } else {
                    std::cout << "Vout = " << dividerOutput(vin, r1, r2) << " V\n";
                }
                break;
            }
            default:
                std::cout << "Unknown option.\n";
        }

        std::cout << "Another calculation? (y/n): ";
        std::cin >> again;
    } while (again == 'y');
}
