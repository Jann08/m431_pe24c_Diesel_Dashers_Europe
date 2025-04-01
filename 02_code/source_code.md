```csharp
using System;
using UnityEngine;
using TMPro;

public class CarController : MonoBehaviour
{
    private float horizontalInput, verticalInput;
    private float currentSteerAngle, currentBreakForce;
    private bool isBreaking;
    private bool isNitroActive;

    // Einstellungen
    [SerializeField] private float motorForce = 1500f;
    [SerializeField] private float breakForce = 3000f;
    [SerializeField] private float maxSteerAngle = 30f;
    [SerializeField] private float maxSpeed = 80f;
    [SerializeField] private float speedDamping = 0.95f;
    [SerializeField] private float steeringSmoothing = 10f;

    // Nitro-Einstellungen
    [SerializeField] private float nitroMultiplier = 2.5f;
    [SerializeField] private float nitroMaxSpeed = 120f;
    [SerializeField] private float nitroForce = 5000f;
    [SerializeField] private AudioSource nitroSound;

    // Wheel Colliders
    [SerializeField] private WheelCollider frontLeftWheelCollider, frontRightWheelCollider;
    [SerializeField] private WheelCollider rearLeftWheelCollider, rearRightWheelCollider;

    // Wheels
    [SerializeField] private Transform frontLeftWheelTransform, frontRightWheelTransform;
    [SerializeField] private Transform rearLeftWheelTransform, rearRightWheelTransform;

    private Rigidbody rb;

    private void Start()
    {
        rb = GetComponent<Rigidbody>();
    }

    private void FixedUpdate()
    {
        GetInput();
        HandleMotor();
        HandleSteering();
        UpdateWheels();
    }

    private void GetInput()
    {
        horizontalInput = Input.GetAxis("Horizontal");
        verticalInput = Input.GetAxis("Vertical");
        isBreaking = Input.GetKey(KeyCode.Space);
        isNitroActive = Input.GetKey(KeyCode.LeftShift);
    }

    private void HandleMotor()
    {
        float speedKmh = rb.velocity.magnitude * 3.6f;

        float currentMaxSpeed = isNitroActive ? nitroMaxSpeed : maxSpeed;
        float currentMotorForce = isNitroActive ? motorForce * nitroMultiplier : motorForce;

        if (speedKmh < currentMaxSpeed)
        {
            frontLeftWheelCollider.motorTorque = verticalInput * currentMotorForce;
            frontRightWheelCollider.motorTorque = verticalInput * currentMotorForce;

            if (isNitroActive)
            {
                rb.AddForce(transform.forward * nitroForce, ForceMode.Acceleration);

                if (nitroSound != null && !nitroSound.isPlaying)
                {
                    nitroSound.Play();
                }
            }
        }
        else
        {
            rb.velocity *= speedDamping;
        }

        if (!isNitroActive && nitroSound != null && nitroSound.isPlaying)
        {
            nitroSound.Stop();
        }

        currentBreakForce = isBreaking ? breakForce : 0f;
        ApplyBreaking();
    }

    private void ApplyBreaking()
    {
        float brake = isBreaking ? currentBreakForce : 0f;
        frontRightWheelCollider.brakeTorque = brake;
        frontLeftWheelCollider.brakeTorque = brake;
        rearLeftWheelCollider.brakeTorque = brake;
        rearRightWheelCollider.brakeTorque = brake;
    }

    private void HandleSteering()
    {
        float speedFactor = Mathf.Clamp01(rb.velocity.magnitude / 20f);
        float adjustedSteering = maxSteerAngle * (1f - 0.7f * speedFactor) * horizontalInput;

        currentSteerAngle = Mathf.Lerp(currentSteerAngle, adjustedSteering, Time.deltaTime * steeringSmoothing);

        frontLeftWheelCollider.steerAngle = currentSteerAngle;
        frontRightWheelCollider.steerAngle = currentSteerAngle;
    }

    private void UpdateWheels()
    {
        UpdateSingleWheel(frontLeftWheelCollider, frontLeftWheelTransform);
        UpdateSingleWheel(frontRightWheelCollider, frontRightWheelTransform);
        UpdateSingleWheel(rearRightWheelCollider, rearRightWheelTransform);
        UpdateSingleWheel(rearLeftWheelCollider, rearLeftWheelTransform);
    }

    private void UpdateSingleWheel(WheelCollider wheelCollider, Transform wheelTransform)
    {
        Vector3 pos;
        Quaternion rot;
        wheelCollider.GetWorldPose(out pos, out rot);
        wheelTransform.rotation = rot;
        wheelTransform.position = pos;
    }
}

public class CarHUD : MonoBehaviour
{
    public CarController carController;
    public TextMeshProUGUI speedText;
    public TextMeshProUGUI gearText;

    private int currentGear = 1;

    void FixedUpdate()
    {
        if (carController == null) return;

        float speedKmh = carController.GetComponent<Rigidbody>().velocity.magnitude * 3.6f;
        speedText.text = $"Speed: {Mathf.Round(speedKmh)} km/h";

        currentGear = CalculateGear(speedKmh);
        gearText.text = $"Gear: {currentGear}";
    }

    private int CalculateGear(float speed)
    {
        if (speed < 5) return 1;
        if (speed < 20) return 2;
        if (speed < 40) return 3;
        if (speed < 60) return 4;
        if (speed < 80) return 5;
        return 6; 
    }
}

public class CameraController : MonoBehaviour
{
    public float rotationSpeed = 3.0f;
    public float zoomSpeed = 5.0f;
    public float minZoom = 5f;
    public float maxZoom = 50f;

    private float currentZoom = 10f;
    private Vector3 lastMousePosition;

    void Update()
    {
        if (Input.GetMouseButton(1))
        {
            Vector3 delta = Input.mousePosition - lastMousePosition;
            float rotationX = delta.x * rotationSpeed * Time.deltaTime;
            float rotationY = delta.y * rotationSpeed * Time.deltaTime;

            transform.Rotate(Vector3.up, rotationX, Space.World);
            transform.Rotate(Vector3.left, rotationY, Space.Self);
        }

        lastMousePosition = Input.mousePosition;

        float scroll = Input.GetAxis("Mouse ScrollWheel");
        currentZoom -= scroll * zoomSpeed;
        currentZoom = Mathf.Clamp(currentZoom, minZoom, maxZoom);
        
        transform.position += transform.forward * scroll * zoomSpeed;
    }
}
```
