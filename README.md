from flask import Flask, jsonify, request

app = Flask(__name__)

doctors = [
    {
        "id": 1,
        "name": "Dr. Smith",
        "schedule": "Evenings",
        "max_patients": 10,
        "available_days": ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"],
    },
    # Add more doctors as needed
]

appointments = []


@app.route("/doctors", methods=["GET"])
def get_doctors():
    return jsonify(doctors)


@app.route("/doctors/<int:doctor_id>", methods=["GET"])
def get_doctor(doctor_id):
    doctor = next((doc for doc in doctors if doc["id"] == doctor_id), None)
    if doctor:
        return jsonify(doctor)
    else:
        return jsonify({"error": "Doctor not found"}), 404


@app.route("/appointments", methods=["POST"])
def book_appointment():
    data = request.json
    doctor_id = data.get("doctor_id")
    if not doctor_id:
        return jsonify({"error": "Doctor ID is required"}), 400

    doctor = next((doc for doc in doctors if doc["id"] == doctor_id), None)
    if not doctor:
        return jsonify({"error": "Doctor not found"}), 404

    if len(appointments) >= doctor["max_patients"]:
        return jsonify({"error": "No available appointments"}), 400

    appointments.append({"doctor_id": doctor_id, "patient_name": data.get("patient_name")})
    return jsonify({"message": "Appointment booked successfully"})


if __name__ == "__main__":
    app.run(debug=True)
