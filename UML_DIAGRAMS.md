# AI-Enhanced IoT Laboratory Management System - UML Diagrams

## 1. USE CASE DIAGRAM

```
@startuml UseCase_SmartLab
left to right direction
skinparam packageBorderColor<<Layer>> LightBlue
skinparam packageStyle<<Layer>> rectangle

actor Admin
actor Engineer
actor Technician
actor LabManager
actor Guest

package "SmartLab System" {

  usecase UC1 as "View Equipment Catalog"
  usecase UC2 as "Checkout Equipment"
  usecase UC3 as "Checkin Equipment"
  usecase UC4 as "Scan QR Code"
  usecase UC5 as "View Checkout History"

  usecase UC6 as "Reserve Equipment"
  usecase UC7 as "View Reservations"
  usecase UC8 as "Approve Reservation"
  usecase UC9 as "Cancel Reservation"

  usecase UC10 as "View Meeting Rooms"
  usecase UC11 as "Book Meeting Room"
  usecase UC12 as "Manage Room Bookings"

  usecase UC13 as "Report Maintenance Issue"
  usecase UC14 as "View Maintenance Tasks"
  usecase UC15 as "Assign Maintenance Task"
  usecase UC16 as "Complete Maintenance Task"

  usecase UC17 as "Manage Inventory"
  usecase UC18 as "Request Parts"
  usecase UC19 as "Approve Parts Request"

  usecase UC20 as "View Analytics"
  usecase UC21 as "View Admin Dashboard"
  usecase UC22 as "Manage Users"
  usecase UC23 as "Manage Labs"
  usecase UC24 as "Manage Storage"

  usecase UC25 as "Query AI Chatbot"
  usecase UC26 as "Voice Search"

  usecase UC27 as "View Notifications"
  usecase UC28 as "Submit Approval Request"
  usecase UC29 as "Review Approval Request"
}

%% Engineer Use Cases
Engineer --> UC1
Engineer --> UC2
Engineer --> UC3
Engineer --> UC4
Engineer --> UC5
Engineer --> UC6
Engineer --> UC7
Engineer --> UC10
Engineer --> UC13
Engineer --> UC14
Engineer --> UC20
Engineer --> UC25
Engineer --> UC26
Engineer --> UC27
Engineer --> UC28

%% Lab Manager Use Cases
LabManager --> UC1
LabManager --> UC2
LabManager --> UC3
LabManager --> UC13
LabManager --> UC14
LabManager --> UC20
LabManager --> UC25
LabManager --> UC27

%% Technician Use Cases
Technician --> UC1
Technician --> UC14
Technician --> UC15
Technician --> UC16
Technician --> UC17
Technician --> UC18
Technician --> UC27

%% Admin Use Cases
Admin --> UC1
Admin --> UC8
Admin --> UC12
Admin --> UC15
Admin --> UC19
Admin --> UC20
Admin --> UC21
Admin --> UC22
Admin --> UC23
Admin --> UC24
Admin --> UC25
Admin --> UC27
Admin --> UC29

%% Guest Use Cases
Guest --> UC1
Guest --> UC10
Guest --> UC25
Guest --> UC27

%% Include relationships
UC2 ..|> UC4 : include
UC3 ..|> UC4 : include
UC6 ..|> UC1 : include
UC8 ..|> UC7 : include
UC12 ..|> UC11 : include
UC16 ..|> UC15 : include
UC29 ..|> UC28 : include

@enduml
```

---

## 2. SEQUENCE DIAGRAM - Equipment Checkout Flow

```
@startuml Sequence_Checkout
actor User
participant "Frontend\n(React)" as Frontend
participant "Checkout\nService" as Service
participant "Local\nStorage" as Storage
participant "Mock\nDatabase" as DB
database "Equipment\nStatus"

User -> Frontend: Input Equipment ID/Scan QR
Frontend -> Service: requestCheckout(equipmentId, userId)
Service -> DB: getEquipmentDetails(equipmentId)
DB --> Service: Equipment Object
Service -> DB: checkAvailability(equipmentId)
DB --> Service: {available: true, status: "available"}

Service -> Storage: saveCheckoutRecord(checkoutData)
Storage --> Service: Success

Service -> DB: updateEquipmentStatus(equipmentId, "checked-out")
DB -> Equipment: Update Status
Equipment --> DB: Confirmed

Service -> DB: createCheckoutRecord(equipment, user, dates)
DB --> Service: Checkout Record {id, status, dates}

Service --> Frontend: {success: true, checkout: record}
Frontend -> Frontend: showConfirmation()
Frontend --> User: Display Checkout Confirmation

User -> Frontend: Confirm Checkout Complete
Frontend -> Service: generateQRReceipt(checkoutId)
Service --> Frontend: Receipt Data with QR
Frontend --> User: Print/Display Receipt

@enduml
```

---

## 3. SEQUENCE DIAGRAM - Equipment Reservation & Approval Flow

```
@startuml Sequence_Reservation
actor User as Engineer
participant "Frontend" as Frontend
participant "Reservation\nService" as ResvService
participant "Approval\nService" as AppService
participant "Notification\nService" as NotifService
participant "Mock\nDatabase" as DB

Engineer -> Frontend: Request Equipment Reservation
Frontend -> ResvService: createReservation(equipmentId, dates)
ResvService -> DB: checkEquipmentAvailability(equipmentId, dates)
DB --> ResvService: availability Status

ResvService -> DB: createReservation({pending, equipment, dates})
DB --> ResvService: Reservation {id, status: "pending"}

ResvService -> AppService: createApprovalRequest(type: "reservation")
AppService -> DB: storeApprovalRequest(approval)
DB --> AppService: Approval {id, status: "pending"}

AppService -> NotifService: notifyApprovers(approval)
NotifService --> Frontend: Notification queued

Frontend --> Engineer: "Reservation submitted for approval"

Note over DB: Time passes...

actor Admin
Admin -> Frontend: View pending approvals
Frontend -> AppService: getPendingApprovals()
AppService -> DB: queryApprovals({status: "pending"})
DB --> AppService: [Approval objects]

AppService --> Frontend: Display approval queue
Frontend --> Admin: Show reservation details

Admin -> Frontend: Approve Reservation
Frontend -> AppService: approveReservation(approvalId)
AppService -> DB: updateApprovalStatus("approved")
DB --> AppService: Updated approval

AppService -> ResvService: updateReservationStatus(reservationId, "approved")
ResvService -> DB: updateReservation({status: "approved"})
DB --> ResvService: Success

AppService -> NotifService: notifyRequester(approved)
NotifService --> Frontend: Send notification to Engineer

Frontend --> Admin: "Reservation approved"
Frontend --> Engineer: "Your reservation was approved!"

@enduml
```

---

## 4. SEQUENCE DIAGRAM - Maintenance Workflow

```
@startuml Sequence_Maintenance
actor Engineer
participant "Frontend" as Frontend
participant "Maintenance\nService" as MaintService
participant "Notification\nService" as NotifService
participant "Mock\nDatabase" as DB

Engineer -> Frontend: Report Maintenance Issue
Frontend -> MaintService: createMaintenanceRequest(equipment, problem)
MaintService -> DB: storeMaintenanceRequest(request)
DB --> MaintService: Request {id, status: "pending"}

MaintService -> DB: updateEquipmentStatus(equipmentId, "maintenance")
DB --> DB: Update equipment

MaintService -> NotifService: notifyTechnicians(request)
NotifService --> Frontend: Notification sent

Frontend --> Engineer: "Issue reported, assigned ID: #123"

actor Technician
Technician -> Frontend: View assigned maintenance tasks
Frontend -> MaintService: getTechnicianTasks(technicianId)
MaintService -> DB: queryMaintenanceRequests({assignedTo: technicianId})
DB --> MaintService: [Maintenance tasks]

MaintService --> Frontend: Display tasks
Frontend --> Technician: Show task details

Technician -> Frontend: Update task - "In Progress"
Frontend -> MaintService: updateMaintenanceTask(taskId, {status: "in-progress"})
MaintService -> DB: updateTask(taskId, updates)
DB --> MaintService: Success

Technician -> Frontend: Complete maintenance - Add notes/photos
Frontend -> MaintService: completeMaintenanceTask(taskId, details)
MaintService -> DB: updateTask({status: "completed", details})
DB --> MaintService: Success

MaintService -> DB: updateEquipmentStatus(equipmentId, "available")
DB --> DB: Equipment back to available

MaintService -> NotifService: notifyReporter(completed)
NotifService --> Frontend: Notification to original reporter

Frontend --> Technician: "Task completed"
Frontend --> Engineer: "Your equipment issue has been resolved"

@enduml
```

---

## 5. CLASS DIAGRAM

```
@startuml ClassDiagram
!define ABSTRACT abstract

package "Domain Models" {

  class User {
    - id: string
    - name: string
    - email: string
    - role: UserRole
    - department: string
    - avatar: string
    - createdAt: Date
    - permissions: Permission[]
    --
    + getRole(): UserRole
    + hasPermission(permission: Permission): boolean
    + canApprove(requestType: string): boolean
  }

  enum UserRole {
    ADMIN
    ENGINEER
    TECHNICIAN
    LAB_MANAGER
    GUEST
  }

  class Equipment {
    - id: string
    - name: string
    - description: string
    - category: EquipmentCategory
    - status: EquipmentStatus
    - location: Location
    - specifications: Map<string, string>
    - qrCode: string
    - usageCount: number
    - lastUsedBy: string
    - lastUsedDate: Date
    --
    + getLocation(): Location
    + isAvailable(): boolean
    + getUsageHistory(): Checkout[]
    + getMaintenanceStatus(): MaintenanceRequest
  }

  enum EquipmentCategory {
    COMPUTER
    MICROCONTROLLER
    SENSOR
    TOOL
    COMPONENT
    OTHER
  }

  enum EquipmentStatus {
    AVAILABLE
    CHECKED_OUT
    MAINTENANCE
    DAMAGED
    RETIRED
  }

  class Location {
    - building: string
    - room: string
    - cabinet: string
    - drawer: string
    - shelf: string
    --
    + getFullAddress(): string
  }

  class Checkout {
    - id: string
    - equipmentId: string
    - userId: string
    - userName: string
    - checkoutDate: Date
    - expectedReturnDate: Date
    - actualReturnDate: Date | null
    - status: CheckoutStatus
    - notes: string
    --
    + isActive(): boolean
    + isOverdue(): boolean
    + getDuration(): number
    + getEquipment(): Equipment
  }

  enum CheckoutStatus {
    ACTIVE
    RETURNED
    OVERDUE
    LOST
  }

  class Reservation {
    - id: string
    - equipmentId: string
    - userId: string
    - startDate: Date
    - endDate: Date
    - status: ReservationStatus
    - approvedBy: string | null
    - rejectionReason: string | null
    --
    + isApproved(): boolean
    + isRejected(): boolean
    + isPending(): boolean
    + conflicts(other: Reservation): boolean
  }

  enum ReservationStatus {
    PENDING
    APPROVED
    REJECTED
    CANCELLED
    FULFILLED
  }

  class MaintenanceRequest {
    - id: string
    - equipmentId: string
    - equipmentName: string
    - problemDescription: string
    - priority: Priority
    - status: MaintenanceStatus
    - assignedTo: string | null
    - reportedBy: string
    - createdDate: Date
    - partsUsed: string[]
    - timeSpent: number
    - photos: string[]
    - location: Location
    --
    + getEquipment(): Equipment
    + isCompleted(): boolean
    + requiresParts(): boolean
    + getAssignedTechnician(): User
  }

  enum MaintenanceStatus {
    PENDING
    IN_PROGRESS
    WAITING_PARTS
    COMPLETED
    CANNOT_REPAIR
  }

  enum Priority {
    LOW
    MEDIUM
    HIGH
  }

  class ApprovalRequest {
    - id: string
    - type: ApprovalType
    - status: ApprovalStatus
    - requester: User
    - reviewedBy: User | null
    - createdDate: Date
    - reviewedDate: Date | null
    - rejectionReason: string | null
    - priority: Priority
    - metadata: Object
    --
    + isPending(): boolean
    + isApproved(): boolean
    + isRejected(): boolean
    + approve(reviewer: User): void
    + reject(reviewer: User, reason: string): void
  }

  enum ApprovalType {
    EQUIPMENT_PURCHASE
    PRODUCT_MODIFICATION
    CHECKOUT_REQUEST
    RESERVATION_REQUEST
    MEETING_ROOM_BOOKING
    LAB_RESERVATION
    STORAGE_ADDITION
  }

  enum ApprovalStatus {
    PENDING
    APPROVED
    REJECTED
    CANCELLED
  }

  class PartInventory {
    - id: string
    - partName: string
    - category: string
    - quantity: number
    - minThreshold: number
    - supplier: string
    - unitCost: number
    - location: Location
    - lastRestockedDate: Date
    --
    + isAvailable(quantity: number): boolean
    + needsRestock(): boolean
    + reserveParts(quantity: number): PartReservation
    + releaseParts(quantity: number): void
  }

  class PartRequest {
    - id: string
    - requesterTechnician: User
    - requestedParts: Part[]
    - justification: string
    - status: RequestStatus
    - approvedBy: User | null
    - priority: Priority
    --
    + isPending(): boolean
    + getPartsList(): Part[]
    + getTotalCost(): number
  }

  enum RequestStatus {
    PENDING
    APPROVED
    ORDERED
    RECEIVED
    REJECTED
  }

  class MeetingRoom {
    - id: string
    - name: string
    - capacity: number
    - building: string
    - floor: number
    - amenities: string[]
    - availability: TimeSlot[]
    --
    + isAvailable(startDate: Date, endDate: Date): boolean
    + book(user: User, dates: DateRange): Booking
    + getUpcomingBookings(): Booking[]
  }

  class Booking {
    - id: string
    - roomId: string
    - userId: string
    - startDate: Date
    - endDate: Date
    - status: BookingStatus
    - notes: string
    --
    + isActive(): boolean
    + cancel(): void
  }

  enum BookingStatus {
    PENDING
    CONFIRMED
    CANCELLED
    COMPLETED
  }

  class Lab {
    - id: string
    - name: string
    - building: string
    - capacity: number
    - supervisor: User
    - equipment: Equipment[]
    - schedule: LabSchedule[]
    --
    + getAvailableSlots(date: Date): TimeSlot[]
    + reserveLab(user: User, dates: DateRange): LabReservation
    + getEquipmentList(): Equipment[]
  }

  class LabReservation {
    - id: string
    - labId: string
    - userId: string
    - startDate: Date
    - endDate: Date
    - status: ReservationStatus
    - purpose: string
    --
    + isApproved(): boolean
  }

  class Notification {
    - id: string
    - userId: string
    - type: NotificationType
    - title: string
    - message: string
    - relatedEntityId: string
    - isRead: boolean
    - createdDate: Date
    --
    + markAsRead(): void
    + getDetails(): Object
  }

  enum NotificationType {
    CHECKOUT_DUE
    RESERVATION_APPROVED
    MAINTENANCE_ASSIGNED
    APPROVAL_REQUEST
    PARTS_AVAILABLE
    ROOM_BOOKING_CONFIRMED
  }

  class ChatbotQuery {
    - id: string
    - userId: string
    - query: string
    - queryType: QueryType
    - response: string
    - timestamp: Date
    - isVoiceInput: boolean
    --
    + processQuery(): string
    + getRelevantEquipment(): Equipment[]
  }

  enum QueryType {
    EQUIPMENT_LOCATION
    EQUIPMENT_AVAILABILITY
    MAINTENANCE_STATUS
    RESERVATION_INFO
    GENERAL_INFO
  }

  class Analytics {
    - id: string
    - userId: string
    - reportType: ReportType
    - dateRange: DateRange
    - data: Map<string, any>
    --
    + generateReport(): Report
    + getEquipmentUtilization(): Map<string, number>
    + getUserActivity(): Map<string, number>
  }

  enum ReportType {
    EQUIPMENT_UTILIZATION
    USER_ACTIVITY
    MAINTENANCE_TRENDS
    RESERVATION_STATS
    INVENTORY_STATUS
  }
}

package "Services" {

  ABSTRACT class BaseService {
    # mockData: Object[]
    --
    + getData(): Object[]
    + create(data: Object): Object
    + update(id: string, data: Object): Object
    + delete(id: string): boolean
  }

  class CheckoutService extends BaseService {
    --
    + checkout(equipmentId, userId): Checkout
    + checkin(checkoutId): Checkout
    + scanQRCode(qrCode): Equipment
    + getCheckoutHistory(userId): Checkout[]
    + getActiveCheckouts(): Checkout[]
  }

  class EquipmentReservationService extends BaseService {
    --
    + createReservation(equipmentId, dates): Reservation
    + getReservations(userId): Reservation[]
    + approveReservation(reservationId): Reservation
    + rejectReservation(reservationId, reason): Reservation
    + cancelReservation(reservationId): void
  }

  class MaintenanceService extends BaseService {
    --
    + reportIssue(equipment, problem): MaintenanceRequest
    + getMaintenanceTasks(): MaintenanceRequest[]
    + assignTask(taskId, technician): MaintenanceRequest
    + updateTaskStatus(taskId, status): MaintenanceRequest
    + completeTask(taskId, details): MaintenanceRequest
  }

  class ApprovalService extends BaseService {
    --
    + createApproval(type, data): ApprovalRequest
    + getPendingApprovals(userId): ApprovalRequest[]
    + approveRequest(approvalId, reviewer): ApprovalRequest
    + rejectRequest(approvalId, reason): ApprovalRequest
    + logActivity(action, user): void
  }

  class PartsService extends BaseService {
    --
    + getInventory(): PartInventory[]
    + requestParts(parts, reason): PartRequest
    + approveParts(requestId): PartRequest
    + updateInventory(partId, quantity): PartInventory
    + checkAvailability(partId, quantity): boolean
  }

  class NotificationService extends BaseService {
    --
    + sendNotification(user, message): Notification
    + getNotifications(userId): Notification[]
    + markAsRead(notificationId): void
    + deleteNotification(notificationId): void
  }

  class UserService extends BaseService {
    --
    + getUserById(userId): User
    + createUser(userData): User
    + updateUser(userId, data): User
    + deleteUser(userId): void
    + getUsersByRole(role): User[]
  }

  class MeetingRoomService extends BaseService {
    --
    + getRooms(): MeetingRoom[]
    + bookRoom(roomId, dates): Booking
    + getRoomAvailability(roomId): TimeSlot[]
    + cancelBooking(bookingId): void
  }

  class LabService extends BaseService {
    --
    + getLabs(): Lab[]
    + reserveLab(labId, dates): LabReservation
    + getLabSchedule(labId): LabSchedule[]
    + approveLabReservation(reservationId): LabReservation
  }
}

package "UI Components" {

  class Header {
    - user: User
    - notifications: Notification[]
    --
    + render(): JSX
    + handleLogout(): void
  }

  class Sidebar {
    - user: User
    - menuItems: MenuItem[]
    --
    + render(): JSX
    + getMenuByRole(role): MenuItem[]
  }

  class NotificationBell {
    - notifications: Notification[]
    - unreadCount: number
    --
    + render(): JSX
    + showNotifications(): void
  }
}

%% Relationships

User "1" -- "0..*" Checkout
User "1" -- "0..*" Reservation
User "1" -- "0..*" MaintenanceRequest
User "1" -- "0..*" ApprovalRequest
User "1" -- "0..*" Notification
User "1" -- "0..*" ChatbotQuery

Equipment "1" -- "0..*" Checkout
Equipment "1" -- "0..*" Reservation
Equipment "1" -- "0..*" MaintenanceRequest
Equipment "1" -- "*" Location

MaintenanceRequest "*" -- "1" PartInventory

Reservation "1" -- "1" Equipment
Reservation "1" -- "1" User

ApprovalRequest "1" -- "1" User
ApprovalRequest "1" -- "0..1" User

MeetingRoom "1" -- "0..*" Booking
Booking "1" -- "1" User

Lab "1" -- "0..*" Equipment
Lab "1" -- "0..*" LabReservation
Lab "1" -- "1" User

Notification "1" -- "1" User

CheckoutService -- Checkout
CheckoutService -- Equipment

EquipmentReservationService -- Reservation
EquipmentReservationService -- ApprovalService

MaintenanceService -- MaintenanceRequest
MaintenanceService -- PartsService

ApprovalService -- ApprovalRequest

PartsService -- PartInventory

MeetingRoomService -- MeetingRoom
MeetingRoomService -- Booking

LabService -- Lab

@enduml
```

---

## Notes on the Diagrams

### Use Case Diagram
- **5 Actor Types**: Admin, Engineer, Technician, Lab Manager, and Guest
- **28 Use Cases**: Covering all major system functionalities
- **Include Relationships**: Show dependencies between use cases (e.g., Checkout includes QR Code scanning)

### Sequence Diagrams
1. **Checkout Flow**: Shows the complete equipment checkout process with QR code generation
2. **Reservation & Approval Flow**: Demonstrates the approval workflow with notifications
3. **Maintenance Workflow**: Shows how maintenance issues are reported, assigned, and resolved

### Class Diagram
- **Domain Models**: Core entities (User, Equipment, Checkout, Reservation, Maintenance, etc.)
- **Enumerations**: Status types and categories for all entities
- **Services**: 8 service classes that handle business logic
- **UI Components**: Header, Sidebar, and Notification components
- **Relationships**: Show associations between classes

---

## How to Use These Diagrams

1. **PlantUML Rendering**: These diagrams use PlantUML syntax and can be rendered using:
   - Online: [PlantUML Online Editor](https://www.plantuml.com/plantuml/uml/)
   - VS Code: Install PlantUML extension
   - GitHub: Some viewers support inline PlantUML rendering

2. **Format Conversion**: You can convert to PNG, SVG, or PDF using PlantUML tools

3. **Update Management**: When modifying the system architecture, update the corresponding diagram sections

---

**Generated for**: AI-Enhanced IoT Laboratory Management System (SmartLab)
**Version**: 1.0.0-alpha
**Date**: 2026-03-05
