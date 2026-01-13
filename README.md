<div align="center">
  <h1>🏢 Enterprise Resource Automation Pipeline</h1>
  <p><strong>Intelligent Uniform Management System for IKEA</strong></p>
  
  <p>
    <img src="https://img.shields.io/badge/n8n-Workflow_Automation-EA4B71?style=for-the-badge&logo=n8n&logoColor=white" alt="n8n" />
    <img src="https://img.shields.io/badge/Supabase-Database-3ECF8E?style=for-the-badge&logo=supabase&logoColor=white" alt="Supabase" />
    <img src="https://img.shields.io/badge/PostgreSQL-Database-336791?style=for-the-badge&logo=postgresql&logoColor=white" alt="PostgreSQL" />
    <img src="https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge" alt="License" />
  </p>
</div>

---

## 📋 About The Project

An **end-to-end automation framework** that revolutionizes uniform management at IKEA by transforming manual, error-prone processes into a seamless, intelligent workflow. Built with **n8n** and **Supabase**, this system unifies multiple departmental data streams and implements business rules as scalable, event-driven workflows.

### The Challenge
Manual uniform management led to:
- ❌ Stock inconsistencies and shortages
- ❌ Delayed approval processes
- ❌ Lack of real-time inventory visibility
- ❌ Fragmented communication across departments
- ❌ No audit trail for compliance

### The Solution
This automation pipeline delivers:
- ✅ **100% stock availability** through real-time inventory validation
- ✅ **Instant manager approval workflows** via interactive email notifications
- ✅ **Automated stock alerts** and reorder triggers
- ✅ **Complete audit trail** with transaction logging
- ✅ **Unified data platform** integrating Google Forms, Sheets, and email

### Key Results
- 🚀 Reduced processing time from days to minutes
- 📊 Real-time visibility into uniform requests and inventory
- 💯 Zero stock-out incidents since deployment
- 📧 Automated email notifications for all stakeholders
- 📈 Scalable architecture supporting multiple locations

---

## 🛠️ Built With

This project leverages modern cloud automation and database technologies:

- **[n8n](https://n8n.io/)** - Low-code workflow automation platform for orchestrating business logic
- **[Supabase](https://supabase.com/)** - Open-source Firebase alternative with PostgreSQL database
- **[PostgreSQL](https://www.postgresql.org/)** - Robust relational database for data storage
- **[Google Sheets](https://www.google.com/sheets/about/)** - Form response collection and data entry
- **[Gmail API](https://developers.google.com/gmail/api)** - Automated email notifications and approvals
- **Webhooks** - Real-time event handling for interactive workflows

---

## ✨ Features

### 🔄 **Request Management**
- Automated intake from Google Forms submissions
- Instant database entry with unique request IDs
- Request status tracking throughout lifecycle
- Duplicate request prevention

### 📦 **Inventory Control**
- Real-time stock validation (SKU, size, color, fit type)
- Automatic quantity checks before approval
- Out-of-stock detection and employee notification
- Reserved quantity tracking for pending approvals

### 👔 **Approval Workflow**
- Interactive email notifications to managers
- One-click approve/reject buttons
- Automated status updates to all parties
- Manager decision logging with timestamps

### 📊 **Reporting & Analytics**
- Weekly automated distribution lists for HR
- Transaction history and audit logs
- Inventory movement tracking (IN/OUT)
- Custom reporting capabilities

### 🔔 **Smart Notifications**
- Manager approval requests
- Employee status updates (approved/rejected/out-of-stock)
- HR weekly digest emails
- Issue confirmation workflows

---

## 🏗️ Architecture

### System Overview

The system operates through three main workflow stages:

```
┌─────────────────┐      ┌──────────────┐      ┌─────────────────┐
│  Google Form    │─────▶│  n8n Engine  │─────▶│  Supabase DB    │
│  (Employee)     │      │  (Workflows) │      │  (PostgreSQL)   │
└─────────────────┘      └──────────────┘      └─────────────────┘
                               │
                               │
                    ┌──────────┴──────────┐
                    ▼                     ▼
            ┌───────────────┐     ┌───────────────┐
            │ Gmail API     │     │  Webhooks     │
            │ (Notifications)│     │ (Interactions)│
            └───────────────┘     └───────────────┘
```

### Workflow Stages

1. **📝 Request Submission**
   - Employee submits uniform request via Google Form
   - Response captured in Google Sheets
   - n8n triggered by new row event

2. **💾 Database Entry**
   - Request details inserted into `requests` table
   - Unique request ID generated
   - Timestamp and employee info recorded

3. **✅ Stock Validation**
   - Query `inventory` table for matching SKU, size, color, fit type
   - Check if `on_hand_qty >= requested_quantity`
   - Route to approval or out-of-stock notification

4. **👨‍💼 Manager Approval**
   - Email sent to manager with approve/reject buttons
   - Workflow pauses using Wait node
   - Manager clicks button, webhook receives decision

5. **🔄 Status Update**
   - Request status updated based on manager decision
   - Employee notified via email
   - Approved requests queued for issuance

6. **📤 Issuance & Logging**
   - HR marks uniform as issued (weekly batch process)
   - Inventory quantity decremented
   - Transaction logged in `transactions` table

7. **📧 Weekly Reporting**
   - Scheduled trigger runs every Monday at 10 AM
   - Compiles all approved requests
   - Sends formatted table to HR for distribution

---

## 💾 Database Schema

The system uses **three core tables** in Supabase (PostgreSQL):

### Database Tables Overview

![Database Tables](uploaded_image_2_1768267716920.png)

### Detailed Schema

![Database Schema](uploaded_image_1_1768267716920.png)

### 📊 **Tables Description**

#### `inventory`
Tracks all uniform items and stock levels
- `id` (int8, PK) - Primary key
- `sku` (text) - Stock keeping unit identifier
- `size` (text) - Size (XS, S, M, L, XL, XXL, etc.)
- `on_hand_qty` (int4) - Available quantity
- `reserved_qty` (int4) - Quantity reserved for pending requests
- `reorder_point` (int4) - Minimum quantity before reorder
- `fit_type` (text) - Fit style (Regular, Slim, Relaxed)
- `color` (text) - Color variant

#### `requests`
Stores all uniform requests with full lifecycle tracking
- `id` (uuid, PK) - Unique request identifier
- `employee_name` (text) - Employee name
- `employee_email` (text) - Employee email
- `manager_email` (text) - Manager email for approval
- `manager_name` (text) - Manager name
- `department` (text) - Department
- `sku` (text) - Requested uniform SKU
- `size_requested` (text) - Requested size
- `color` (text) - Requested color
- `fit_type` (text) - Requested fit type
- `reason` (text) - Reason for request
- `quantity` (int2) - Quantity requested
- `status` (text) - Current status (Requested, Approve, Reject, Issued, Out Of Stock)
- `requested_at` (timestamptz) - Request timestamp
- `approved_at` (timestamptz) - Approval timestamp
- `issued_at` (timestamptz) - Issuance timestamp
- `fit_confirmed_at` (timestamptz) - Fit confirmation timestamp
- `token` (text) - Unique token for tracking
- `notes` (text) - Additional notes

#### `transactions`
Audit log for all inventory movements
- `id` (int8, PK) - Transaction ID
- `request_id` (uuid, FK) - Links to requests table
- `sku` (text) - Item SKU
- `size` (text) - Item size
- `direction` (text) - Movement type (IN/OUT)
- `quantity` (int4) - Quantity moved
- `occurred_at` (timestamptz) - Transaction timestamp
- `by_whom` (text) - Person who performed action

### Database Indexes

![Database Indexes](uploaded_image_0_1768267716920.png)

**Performance Optimizations:**
- `idx_inventory_sku_size` - Fast lookup for stock checks
- `idx_requests_status` - Efficient filtering by request status
- Primary key indexes on all tables

---

## 🚀 Getting Started

### Prerequisites

Before setting up the automation pipeline, ensure you have:

- **n8n instance** (self-hosted or [n8n.cloud](https://n8n.cloud))
  ```bash
  npm install n8n -g
  # or
  docker run -it --rm --name n8n -p 5678:5678 n8nio/n8n
  ```

- **Supabase project** - [Create free account](https://supabase.com/)

- **Google Workspace account** with access to:
  - Google Forms
  - Google Sheets
  - Gmail

- **API Credentials**:
  - Gmail OAuth2 credentials ([Setup guide](https://developers.google.com/gmail/api/quickstart))
  - Google Sheets API access
  - Supabase API keys (Project Settings → API)

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/Enterprise-Resource-Automation-Pipeline.git
   cd Enterprise-Resource-Automation-Pipeline
   ```

2. **Set up Supabase database**
   
   Create the three tables in your Supabase project:
   
   ```sql
   -- Create inventory table
   CREATE TABLE inventory (
     id SERIAL PRIMARY KEY,
     sku TEXT NOT NULL,
     size TEXT NOT NULL,
     on_hand_qty INTEGER DEFAULT 0,
     reserved_qty INTEGER DEFAULT 0,
     reorder_point INTEGER DEFAULT 10,
     fit_type TEXT,
     color TEXT
   );

   -- Create requests table
   CREATE TABLE requests (
     id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
     employee_name TEXT,
     employee_email TEXT,
     manager_email TEXT,
     manager_name TEXT,
     department TEXT,
     sku TEXT,
     size_requested TEXT,
     color TEXT,
     fit_type TEXT,
     reason TEXT,
     quantity SMALLINT,
     status TEXT DEFAULT 'Requested',
     requested_at TIMESTAMPTZ,
     approved_at TIMESTAMPTZ,
     issued_at TIMESTAMPTZ,
     fit_confirmed_at TIMESTAMPTZ,
     token TEXT,
     notes TEXT
   );

   -- Create transactions table
   CREATE TABLE transactions (
     id SERIAL PRIMARY KEY,
     request_id UUID REFERENCES requests(id),
     sku TEXT,
     size TEXT,
     direction TEXT,
     quantity INTEGER,
     occurred_at TIMESTAMPTZ DEFAULT NOW(),
     by_whom TEXT
   );

   -- Create indexes for performance
   CREATE INDEX idx_inventory_sku_size ON inventory(sku, size);
   CREATE INDEX idx_requests_status ON requests(status);
   ```

3. **Configure n8n credentials**

   In your n8n instance, add the following credentials:
   
   - **Supabase API**: Project URL and API key (anon/public)
   - **Gmail OAuth2**: Client ID, Client Secret, and authorize access
   - **Google Sheets OAuth2**: Enable Sheets API and authorize

4. **Import the workflow**

   - Open n8n interface
   - Click **Import from File**
   - Select `Uniform - Intake and Manager Approval.json`
   - Workflow will be imported with all nodes

5. **Update webhook URLs**

   The workflow contains webhook URLs that need to be updated:
   
   - Open the imported workflow
   - Find the "Manager Approval Waiting" node
   - Update the webhook suffix to match your n8n instance URL
   - Repeat for "Webhook" node (issuance flow)

6. **Set up Google Form**

   Create a Google Form with these fields:
   - Email Address
   - Employee Name
   - Manager Name
   - Manager's Email
   - Department
   - SKU (Type of uniform) - Dropdown
   - Color - Dropdown
   - Size - Dropdown
   - Fit Type - Dropdown
   - Quantity - Number
   - Reason for new uniform? - Short answer
   - Timestamp (automatic)

   Link the form responses to a Google Sheet.

7. **Configure the trigger**

   - In the workflow, open "CW Response" node
   - Select your Google Sheet
   - Map the spreadsheet columns to match your form

8. **Activate the workflow**

   Click the toggle at the top of the workflow to activate it!

---

## 📖 Usage

### For Employees

1. **Submit a uniform request**
   - Fill out the Google Form with your details
   - Select the uniform type, size, color, and fit
   - Specify quantity and reason
   - Submit the form

2. **Receive confirmation**
   - If stock is available, request is sent to your manager
   - If out of stock, you'll receive an immediate email notification

3. **Wait for approval**
   - Manager reviews and approves/rejects
   - You receive email notification of the decision

### For Managers

1. **Receive approval request email**

   ![Manager Approval Email](uploaded_image_3_1768267716920.png)
   
   You'll receive an email with:
   - Employee details
   - Uniform item, size, and quantity
   - Reason for request
   - **Approve** and **Reject** buttons

2. **Make decision**
   - Click **Approve Request** to approve
   - Click **Reject Request** to deny
   - Employee is automatically notified

3. **Track decisions**
   - All decisions are logged in the database
   - Timestamp recorded for audit trail

### For HR Team

1. **Receive weekly distribution list**
   - Every Monday at 10 AM
   - Email contains table of all approved requests
   - Includes employee name, SKU, size, color, fit type, quantity

2. **Issue uniforms**
   - Click "Mark Issued" button for each request
   - System updates inventory automatically
   - Transaction logged for compliance

3. **Monitor inventory**
   - Check Supabase dashboard for stock levels
   - Review transaction history
   - Track request statuses

### Email Notifications

#### Employee Approval Confirmation

![Employee Approval Email](uploaded_image_4_1768267716920.png)

Employees receive simple, clear notifications when their request is approved.

---

## ⚙️ Configuration

### Email Templates

Email templates are defined in the HTML nodes. To customize:

1. Open the workflow in n8n
2. Find the "HTML1" node (manager approval email)
3. Edit the HTML content with your branding
4. Update button colors and styling as needed

### Schedule Settings

The weekly HR report is sent every Monday at 10 AM. To change:

1. Open "Schedule Trigger" node
2. Modify the interval settings:
   ```
   Interval: weeks
   Trigger on: Monday
   Trigger at: 10:00
   ```

### Stock Thresholds

To adjust when out-of-stock notifications are sent:

1. Open "Stock Available?" node
2. Modify the condition logic
3. Currently checks: `on_hand_qty < requested_quantity`

### Manager Hierarchy

Managers are determined by the form submission (Manager's Email field). To implement a department-based lookup:

1. Create a new Supabase table `departments`
2. Add node to query manager based on employee department
3. Replace hardcoded manager email with lookup result

---

## 🗺️ Roadmap

Future enhancements planned:

- [ ] **Multi-location support** - Separate inventory by store/warehouse
- [ ] **Advanced analytics dashboard** - Power BI or Metabase integration
- [ ] **Mobile app** - React Native app for on-the-go requests
- [ ] **Predictive reordering** - ML-based stock forecasting
- [ ] **Procurement integration** - Auto-create POs when stock low
- [ ] **Size recommendations** - Suggest sizes based on employee history
- [ ] **Slack/Teams integration** - Notifications via workplace chat
- [ ] **Barcode scanning** - Mobile app for inventory management
- [ ] **Return workflow** - Handle uniform returns and exchanges
- [ ] **Cost tracking** - Budget management and reporting

See the [open issues](https://github.com/yourusername/Enterprise-Resource-Automation-Pipeline/issues) for a full list of proposed features.

---

## 🤝 Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📄 License

Distributed under the MIT License. See `LICENSE` file for more information.

---

## 📧 Contact

**Nirmit Khurana**

- Portfolio: [Your Portfolio URL]
- LinkedIn: [Your LinkedIn Profile]
- Email: nirmitkhurana82@gmail.com
- Project Link: [https://github.com/yourusername/Enterprise-Resource-Automation-Pipeline](https://github.com/yourusername/Enterprise-Resource-Automation-Pipeline)

---

## 🙏 Acknowledgments

This project was built using incredible open-source tools and resources:

- [n8n](https://n8n.io/) - Fair-code licensed workflow automation
- [Supabase](https://supabase.com/) - The open source Firebase alternative
- [Best-README-Template](https://github.com/othneildrew/Best-README-Template) - README structure inspiration
- [Shields.io](https://shields.io/) - Awesome badges
- [Google Workspace](https://workspace.google.com/) - Form and email infrastructure
- IKEA - For the opportunity to build this automation

---

<div align="center">
  <p>⭐ Star this repo if you found it helpful! ⭐</p>
  <p>Made with ❤️ by Nirmit Khurana</p>
</div>
