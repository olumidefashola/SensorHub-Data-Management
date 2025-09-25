# SensorHub Data Management ���

A decentralized IoT data management system built with Clarity smart contracts that enables secure sensor registration, data collection, verification, and monetization. Perfect for IoT networks, smart cities, environmental monitoring, and data marketplace applications.

## Overview

SensorHub Data Management creates a trustless ecosystem where IoT sensor owners can register devices, collect and monetize data, while data consumers can access real-time sensor feeds through subscription-based models. The system includes data verification, reputation scoring, and automated payment distribution.

## Features

- **��� Sensor Registration**: Comprehensive IoT device registration and management
- **��� Data Monetization**: Subscription-based data access with automated payments
- **��� Data Verification**: Community-based data validation system
- **⭐ Reputation System**: Sensor reliability scoring based on data quality
- **��� Stake-Based Security**: Sensor owners stake tokens to ensure data quality
- **⏰ Real-Time Data**: Fresh data tracking with expiration windows
- **��� Multi-Sensor Types**: Support for various sensor categories

## Smart Contract Architecture

### Core Components

1. **Sensor Registry**: Device registration and metadata management
2. **Data Storage**: Timestamped sensor readings with hash verification
3. **Subscription System**: Pay-per-query and time-based access models
4. **Verification Network**: Community validation of sensor data
5. **Reputation Engine**: Quality scoring for sensors and data

### Sensor Types Supported

- `TYPE-TEMPERATURE (1)`: Temperature sensors
- `TYPE-HUMIDITY (2)`: Humidity sensors  
- `TYPE-AIR-QUALITY (3)`: Air quality monitors
- `TYPE-MOTION (4)`: Motion/presence detectors
- `TYPE-SOUND (5)`: Sound level meters
- `TYPE-LIGHT (6)`: Light/luminosity sensors

## Getting Started

### Prerequisites

- Clarinet for local development and testing
- Understanding of Clarity smart contract language
- Knowledge of IoT systems and data management

### Installation

1. Clone the repository:
```bash
git clone https://github.com/your-org/sensorhub-data
cd sensorhub-data
```

2. Initialize Clarinet project:
```bash
clarinet new sensorhub-data
cd sensorhub-data
```

3. Add the contract to your `Clarinet.toml`:
```toml
[contracts.sensor-registry]
path = "contracts/sensor-registry.clar"
```

### Deployment

1. Test the contract:
```bash
clarinet test
```

2. Deploy to testnet:
```bash
clarinet deploy --testnet
```

## Usage Guide

### 1. Deposit Funds

All participants need to deposit STX for operations:

```clarity
;; Deposit funds for sensor registration, subscriptions, or rewards
(contract-call? .sensor-registry deposit u50000) ;; 50,000 microSTX
```

### 2. Register IoT Sensor

Sensor owners register their devices:

```clarity
(contract-call? .sensor-registry register-sensor 
  "TEMP-SENSOR-NYC-001"                    ;; device ID
  u1                                       ;; temperature sensor type
  "New York, NY - Central Park"           ;; location
  "High-precision temperature monitoring" ;; description
  u100                                     ;; price per query (microSTX)
  u15000)                                  ;; stake amount
```

### 3. Submit Sensor Data

Registered sensors submit periodic data:

```clarity
;; Submit temperature reading (in celsius * 100 for precision)
(contract-call? .sensor-registry submit-data 
  u1                              ;; sensor ID
  2350                           ;; data value (23.50°C)
  0x1234567890abcdef...)         ;; data hash for verification
```

### 4. Subscribe to Data Feed

Data consumers purchase sensor access:

```clarity
(contract-call? .sensor-registry subscribe-to-sensor 
  u1          ;; sensor ID
  u1440       ;; duration (10 days in blocks)
  u100)       ;; maximum queries allowed
```

### 5. Query Sensor Data

Subscribers access real-time data:

```clarity
;; Query latest sensor reading
(contract-call? .sensor-registry query-sensor-data u1)
```

### 6. Verify Data Quality

Community validators verify sensor data:

```clarity
;; Verify data entry as accurate
(contract-call? .sensor-registry verify-data u1 true)
```

### 7. Monitor Status

Check sensor and subscription status:

```clarity
;; Get sensor information
(contract-call? .sensor-registry get-sensor u1)

;; Check subscription details
(contract-call? .sensor-registry get-subscription tx-sender u1)

;; Verify sensor is active and providing fresh data
(contract-call? .sensor-registry is-sensor-active u1)
```

## Configuration

### System Parameters

- **Minimum Stake**: `10,000` microSTX per sensor
- **Platform Fee**: `2%` of subscription payments
- **Data Freshness Window**: `1 day` (144 blocks)
- **Data Retention Period**: `1 year` (52,560 blocks)
- **Max Sensors Per Owner**: `100` devices

### Administrative Settings

```clarity
;; Update platform fee (max 10%)
(contract-call? .sensor-registry set-platform-fee u300) ;; 3%

;; Adjust data retention period
(contract-call? .sensor-registry set-data-retention u26280) ;; 6 months
```

## Data Management Workflow

### Sensor Lifecycle
1. **Registration** → Owner stakes tokens and registers device
2. **Data Collection** → Sensor submits periodic readings
3. **Monetization** → Consumers purchase data subscriptions
4. **Verification** → Community validates data quality
5. **Reputation Building** → Quality scores improve market value
6. **Deactivation** → Owner can deactivate and reclaim stake

### Revenue Distribution
- **Sensor Owner**: Subscription fees minus platform fee
- **Platform**: 2% fee for infrastructure and development
- **Validators**: Reputation-based rewards (future enhancement)

## Security Features

### Stake-Based Quality Assurance
- Sensor owners stake minimum amounts
- Stakes are at risk for poor data quality
- Reputation affects future earning potential

### Data Integrity
- Hash-based data verification
- Community validation system
- Timestamp-based freshness tracking
- Automated expiration management

### Access Controls
- Owner-only sensor management
- Subscription-based data access
- Validator restrictions prevent self-verification
- Administrative controls for platform management

## Error Handling

Comprehensive error management:

- `u100`: Unauthorized access attempt
- `u101`: Sensor or data not found
- `u102`: Resource already exists
- `u103`: Invalid input parameters
- `u104`: Insufficient funds for operation
- `u105`: Sensor offline or inactive
- `u106`: Data too old or expired
- `u107`: Subscription expired or invalid

## IoT Use Cases

### Smart Cities
- Traffic monitoring sensors
- Air quality networks
- Noise pollution tracking
- Public utility monitoring

### Environmental Monitoring
- Weather station networks
- Pollution monitoring
- Agricultural sensors
- Wildlife tracking

### Industrial IoT
- Manufacturing sensor networks
- Supply chain monitoring
- Equipment health tracking
- Energy consumption monitoring

### Consumer Applications
- Home automation sensors
- Health monitoring devices
- Smart building systems
- Personal environmental tracking

## Testing

### Unit Tests

```clarity
;; Test sensor registration
(define-test test-register-sensor
  (begin
    (unwrap! (contract-call? .sensor-registry deposit u20000) false)
    (let ((sensor-id (unwrap! (contract-call? .sensor-registry register-sensor 
                                "TEST-TEMP-001" u1 "Test Location"
                                "Test temperature sensor" u50 u15000) false)))
      (is-eq sensor-id u1))))

;; Test data submission
(define-test test-submit-data
  (begin
    (unwrap! (contract-call? .sensor-registry deposit u20000) false)
    (let ((sensor-id (unwrap! (contract-call? .sensor-registry register-sensor 
                                "TEST-TEMP-001" u1 "Test Location"
                                "Test temperature sensor" u50 u15000) false)))
      (unwrap! (contract-call? .sensor-registry submit-data 
                 sensor-id 2500 0x1234567890abcdef) false))))
```

## Data Privacy & Security

### Privacy Features
- Hash-based data integrity without revealing raw data
- Subscription-based access control
- Selective data sharing capabilities
- Owner-controlled sensor activation

### Security Measures
- Stake-based sensor registration
- Multi-party data verification
- Time-based access controls
- Reputation-based trust scoring

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Roadmap

- [ ] Integration with IPFS for large data storage
- [ ] Machine learning-based data validation
- [ ] Multi-token support for payments
- [ ] Mobile SDK for sensor integration
- [ ] Dashboard and analytics platform
- [ ] Cross-platform sensor APIs

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- IoT industry standards and protocols
- Decentralized data management research
- Clarity language documentation
- Blockchain IoT integration studies

## Support

For questions and support:

- Create an issue in this repository
- Join our community Discord
- Check the documentation wiki

---

**Built with ��� for the connected world**
