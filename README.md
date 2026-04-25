

Uploaded image
this is our layout on C#, can u help me create a C# code using visual studio 2012 it should be funtional and also has a datagridview for Transaction history Upgrade,we actually want to be the same function like bermorzone.ph but on C# version.

as you can see on the picture we have step by step

can u help us create it by making a step by step we are using windows form application.

Got it — your sketch is actually a solid flow already. I’ll turn that into a working Windows Forms (VS 2012) step-by-step system with:

Step-by-step PC builder (like your drawing)

Database (SQL Server)

Transaction history (DataGridView)

Basic purchase flow

I’ll keep it practical so you can build it directly.

🧱 1. Create the Project
Open Visual Studio 2012

New Project → Windows Forms Application

Name it: PCBuilderApp

🗄️ 2. Create Database (SQL Server)
Create a database (LocalDB or SQL Express)

Tables:
CREATE TABLE Components (
    Id INT PRIMARY KEY IDENTITY,
    Category VARCHAR(50),
    Brand VARCHAR(50),
    Model VARCHAR(100),
    Price DECIMAL(10,2)
);

CREATE TABLE Transactions (
    Id INT PRIMARY KEY IDENTITY,
    Username VARCHAR(50),
    ContactNumber VARCHAR(20),
    ProductSummary VARCHAR(MAX),
    TotalPrice DECIMAL(10,2),
    DateCreated DATETIME DEFAULT GETDATE()
);
🧩 3. Forms You Need (based on your sketch)
Create these Forms:

MainForm.cs → Navigation (Next Build)

BuilderForm.cs → Step-by-step selection

ProductForm.cs → Summary of selected parts

CheckoutForm.cs → User info + purchase

TransactionHistoryForm.cs → DataGridView

🧭 4. MainForm (Navigation)
Add buttons:

btnStartBuild

btnTransactions

private void btnStartBuild_Click(object sender, EventArgs e)
{
    BuilderForm bf = new BuilderForm();
    bf.Show();
}

private void btnTransactions_Click(object sender, EventArgs e)
{
    TransactionHistoryForm tf = new TransactionHistoryForm();
    tf.Show();
}
⚙️ 5. BuilderForm (Step-by-step like your drawing)
Controls:
ComboBox: cbMotherboard

ComboBox: cbProcessor

ComboBox: cbGPU

ComboBox: cbRAM

Button: btnNext

Load Data from DB:
string connStr = @"Data Source=.\SQLEXPRESS;Initial Catalog=PCBuilderDB;Integrated Security=True";

private void LoadComponents(string category, ComboBox cb)
{
    using (SqlConnection conn = new SqlConnection(connStr))
    {
        conn.Open();
        SqlCommand cmd = new SqlCommand("SELECT Model FROM Components WHERE Category=@cat", conn);
        cmd.Parameters.AddWithValue("@cat", category);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            cb.Items.Add(reader["Model"].ToString());
        }
    }
}
Call it:
private void BuilderForm_Load(object sender, EventArgs e)
{
    LoadComponents("Motherboard", cbMotherboard);
    LoadComponents("Processor", cbProcessor);
    LoadComponents("GPU", cbGPU);
    LoadComponents("RAM", cbRAM);
}
Pass Data to Next Form
private void btnNext_Click(object sender, EventArgs e)
{
    ProductForm pf = new ProductForm();

    pf.motherboard = cbMotherboard.Text;
    pf.processor = cbProcessor.Text;
    pf.gpu = cbGPU.Text;
    pf.ram = cbRAM.Text;

    pf.Show();
}
📦 6. ProductForm (Summary)
Variables:
public string motherboard, processor, gpu, ram;
Show Summary:
private void ProductForm_Load(object sender, EventArgs e)
{
    lblSummary.Text =
        "Motherboard: " + motherboard + "\n" +
        "Processor: " + processor + "\n" +
        "GPU: " + gpu + "\n" +
        "RAM: " + ram;
}
Next → Checkout
private void btnCheckout_Click(object sender, EventArgs e)
{
    CheckoutForm cf = new CheckoutForm();
    cf.productSummary = lblSummary.Text;
    cf.Show();
}
💳 7. CheckoutForm
Controls:
TextBox: txtUsername

TextBox: txtNumber

Label: lblProduct

Button: btnPurchase

Code:
public string productSummary;

private void CheckoutForm_Load(object sender, EventArgs e)
{
    lblProduct.Text = productSummary;
}
Save Transaction:
private void btnPurchase_Click(object sender, EventArgs e)
{
    using (SqlConnection conn = new SqlConnection(connStr))
    {
        conn.Open();
        SqlCommand cmd = new SqlCommand(
            "INSERT INTO Transactions (Username, ContactNumber, ProductSummary, TotalPrice) VALUES (@u,@n,@p,@t)", conn);

        cmd.Parameters.AddWithValue("@u", txtUsername.Text);
        cmd.Parameters.AddWithValue("@n", txtNumber.Text);
        cmd.Parameters.AddWithValue("@p", productSummary);
        cmd.Parameters.AddWithValue("@t", 0); // you can compute later

        cmd.ExecuteNonQuery();
    }

    MessageBox.Show("Purchase Successful!");
}
📊 8. TransactionHistoryForm (DataGridView)
Add:
DataGridView → dataGridView1

Load Data:
private void TransactionHistoryForm_Load(object sender, EventArgs e)
{
    using (SqlConnection conn = new SqlConnection(connStr))
    {
        conn.Open();
        SqlDataAdapter da = new SqlDataAdapter("SELECT * FROM Transactions", conn);
        DataTable dt = new DataTable();
        da.Fill(dt);

        dataGridView1.DataSource = dt;
    }
}
🧠 9. Optional Upgrades (Recommended)
To match Bermor-style system better:

✔ Add price calculation

✔ Add images (PictureBox)

✔ Add search bar

✔ Add account login system

✔ Add component compatibility check

🧩 How Your Sketch Maps to Code
Your drawing → Implementation:

“Next Build” → MainForm

“PC Components → Motherboard/CPU…” → BuilderForm

“ASUS → ROG STRIX Z490” → ComboBox selection

“Product → Purchase Verification” → ProductForm

“Username + Number → Purchase” → CheckoutForm

Transaction list → DataGridView

🚀 If you want next
I can:

Design the actual UI layout (drag-drop positions)

Add price computation system

Add login/account system

Or generate a complete ready-to-copy project code

Just tell me 👍


can u create a layout of it?

Yeah — I’ll turn your sketch into a clean Windows Forms layout (drag-and-drop guide) so you can build it in Visual Studio 2012 easily.

I’ll show each form visually (like a wireframe) + what controls to place.

🖥️ 1. MAIN FORM (Dashboard)
----------------------------------------
|        PC BUILDER SYSTEM              |
|--------------------------------------|
|                                      |
|     [ Start New Build ]              |
|                                      |
|     [ Transaction History ]          |
|                                      |
|     [ Account Settings ]             |
|                                      |
----------------------------------------
Controls:
Label → Title

Button → btnStartBuild

Button → btnTransactions

Button → btnAccount

👉 Center everything for clean UI

⚙️ 2. BUILDER FORM (Step-by-Step)
-------------------------------------------------
|                PC BUILDER                      |
|-----------------------------------------------|
| Motherboard:   [ComboBox ▼]                   |
| Processor:     [ComboBox ▼]                   |
| GPU:           [ComboBox ▼]                   |
| RAM:           [ComboBox ▼]                   |
|                                               |
| [ Search Component 🔍 ]                       |
|                                               |
|                 [ NEXT ➜ ]                   |
-------------------------------------------------
Controls:
4 Labels

4 ComboBoxes:

cbMotherboard

cbProcessor

cbGPU

cbRAM

TextBox → txtSearch

Button → btnSearch

Button → btnNext

👉 Tip: Align labels left, combo boxes same width

📦 3. PRODUCT SUMMARY FORM
-------------------------------------------------
|             BUILD SUMMARY                      |
|-----------------------------------------------|
| Motherboard: ASUS ROG STRIX Z490              |
| Processor: Intel i7                           |
| GPU: RTX 3060                                 |
| RAM: 16GB                                     |
|-----------------------------------------------|
| Total Price: ₱ 45,000                         |
|                                               |
|     [ BACK ]      [ CHECKOUT ]                |
-------------------------------------------------
Controls:
Label → lblSummary

Label → lblTotal

Button → btnBack

Button → btnCheckout

👉 Use multiline label or RichTextBox for summary

💳 4. CHECKOUT FORM
-------------------------------------------------
|               CHECKOUT                         |
|-----------------------------------------------|
| Username:        [______________]             |
| Contact Number:  [______________]             |
|                                               |
| Product:                                      |
| -------------------------------------------  |
| ASUS ROG STRIX Z490                          |
| Intel i7                                     |
| RTX 3060                                     |
| 16GB RAM                                     |
| -------------------------------------------  |
|                                               |
|            [ PURCHASE ]                       |
-------------------------------------------------
Controls:
TextBox → txtUsername

TextBox → txtNumber

Label / RichTextBox → lblProduct

Button → btnPurchase

📊 5. TRANSACTION HISTORY FORM
-------------------------------------------------
|          TRANSACTION HISTORY                  |
|-----------------------------------------------|
| [ Search: __________ ] [ Refresh ]            |
|                                               |
| -------------------------------------------  |
| | ID | Name | Product | Price | Date |       |
| |----|------|---------|-------|------|       |
| | 1  | John | PC Set  | 45k   | ...  |       |
| | 2  | Ana  | PC Set  | 38k   | ...  |       |
| -------------------------------------------  |
|                                               |
-------------------------------------------------
Controls:
DataGridView → dataGridView1

TextBox → txtSearch

Button → btnRefresh

👉 Set:

dataGridView1.AutoSizeColumnsMode = DataGridViewAutoSizeColumnsMode.Fill;
🎨 DESIGN TIPS (Important)
To make it feel like Bermor:

Use dark theme

Background: dark gray

Buttons: blue or green

Font:

Title: Bold (16–20pt)

Buttons:

FlatStyle = Flat

Add:

Panel containers for sections

🔄 FLOW (How it connects)
MainForm
   ↓
BuilderForm
   ↓
ProductForm
   ↓
CheckoutForm
   ↓
TransactionHistoryForm
🚀 If you want next step
I can:

Turn this into actual drag-and-drop coordinates

Generate full working .cs code with UI

Or design a modern UI (like Bermor)

Just tell me 👍


CREATE TABLE Components (
    Id INT PRIMARY KEY IDENTITY,
    Category VARCHAR(50),
    Brand VARCHAR(50),
    Model VARCHAR(100),
    Price DECIMAL(10,2)
);

CREATE TABLE Transactions (
    Id INT PRIMARY KEY IDENTITY,
    Username VARCHAR(50),
    ContactNumber VARCHAR(20),
    ProductSummary VARCHAR(MAX),
    TotalPrice DECIMAL(10,2),
    DateCreated DATETIME DEFAULT GETDATE()
);

should i make 2 table for this? but if it need 2 table i want it to be shown on 1 datagridview

Short answer: Yes, you should keep 2 tables 👍 — that’s actually the correct design.

But don’t worry — you can still show everything in ONE DataGridView using a JOIN query.

🧠 Why 2 Tables is Correct
Components → stores all PC parts (Motherboard, CPU, etc.)

Transactions → stores purchases

👉 If you combine them into one table, it becomes messy and hard to manage.

🔥 The Real Fix (What you're missing)
Right now your Transactions table only stores:

ProductSummary VARCHAR(MAX)
❌ Problem: You cannot properly track each component

✅ Better Database Design (Recommended)
Add a 3rd table (VERY IMPORTANT):

CREATE TABLE TransactionDetails (
    Id INT PRIMARY KEY IDENTITY,
    TransactionId INT,
    ComponentId INT,
    FOREIGN KEY (TransactionId) REFERENCES Transactions(Id),
    FOREIGN KEY (ComponentId) REFERENCES Components(Id)
);
🧩 Now You Have:
Table	Purpose
Components	All PC parts
Transactions	Customer purchase
TransactionDetails	What items were bought
📊 Show Everything in ONE DataGridView
Now you can JOIN them 👇

SELECT 
    t.Id,
    t.Username,
    t.ContactNumber,
    c.Category,
    c.Brand,
    c.Model,
    c.Price,
    t.DateCreated
FROM Transactions t
JOIN TransactionDetails td ON t.Id = td.TransactionId
JOIN Components c ON td.ComponentId = c.Id
💻 C# Code for DataGridView
string query = @"
SELECT 
    t.Id,
    t.Username,
    t.ContactNumber,
    c.Category,
    c.Brand,
    c.Model,
    c.Price,
    t.DateCreated
FROM Transactions t
JOIN TransactionDetails td ON t.Id = td.TransactionId
JOIN Components c ON td.ComponentId = c.Id";

using (SqlConnection conn = new SqlConnection(connStr))
{
    conn.Open();
    SqlDataAdapter da = new SqlDataAdapter(query, conn);
    DataTable dt = new DataTable();
    da.Fill(dt);

    dataGridView1.DataSource = dt;
}
🧾 Example Output in DataGridView
ID | Username | Category   | Model              | Price
--------------------------------------------------------
1  | John     | CPU        | Intel i5           | 12000
1  | John     | GPU        | RTX 3060           | 25000
1  | John     | RAM        | 16GB DDR4          | 5000
👉 Notice: same transaction ID = same build

⚠️ If You Stay With ONLY 2 Tables
You can still show 1 DataGridView, BUT:

SELECT * FROM Transactions
👉 It will look like:

One row per purchase

Components are just TEXT (not flexible)

💡 My Recommendation (Important)
✔ Use 3 tables (best like real systems like Bermor)
✔ Use JOIN to display in 1 DataGridView
✔ Avoid storing everything in ProductSummary

🚀 If you want next
I can help you:

✔ Insert selected components into TransactionDetails

✔ Auto compute total price

✔ Make it exactly like Bermor (step-by-step logic)

Just tell me 👍


how to join them? should i make 3 tables for it? can u show me what should be the output

Yes — you should make 3 tables 👍
That’s the correct and professional way (even real systems like Bermor use this structure).

Now I’ll show you clearly:

🧱 1. FINAL DATABASE STRUCTURE (What you should have)
✅ Table 1: Components
CREATE TABLE Components (
    Id INT PRIMARY KEY IDENTITY,
    Category VARCHAR(50),
    Brand VARCHAR(50),
    Model VARCHAR(100),
    Price DECIMAL(10,2)
);
✅ Table 2: Transactions (one per customer purchase)
CREATE TABLE Transactions (
    Id INT PRIMARY KEY IDENTITY,
    Username VARCHAR(50),
    ContactNumber VARCHAR(20),
    TotalPrice DECIMAL(10,2),
    DateCreated DATETIME DEFAULT GETDATE()
);
✅ Table 3: TransactionDetails (IMPORTANT)
CREATE TABLE TransactionDetails (
    Id INT PRIMARY KEY IDENTITY,
    TransactionId INT,
    ComponentId INT,
    FOREIGN KEY (TransactionId) REFERENCES Transactions(Id),
    FOREIGN KEY (ComponentId) REFERENCES Components(Id)
);
🧠 2. HOW IT WORKS (Simple Explanation)
Let’s say user builds a PC:

CPU → Intel i5

GPU → RTX 3060

RAM → 16GB

What gets saved:
Transactions
Id | Username | TotalPrice
1  | John     | 42000
TransactionDetails
Id | TransactionId | ComponentId
1  | 1             | 3   (CPU)
2  | 1             | 7   (GPU)
3  | 1             | 10  (RAM)
🔗 3. THE JOIN (THIS IS WHAT YOU NEED)
This combines ALL tables into ONE result 👇

SELECT 
    t.Id AS TransactionID,
    t.Username,
    t.ContactNumber,
    c.Category,
    c.Brand,
    c.Model,
    c.Price,
    t.DateCreated
FROM Transactions t
INNER JOIN TransactionDetails td ON t.Id = td.TransactionId
INNER JOIN Components c ON td.ComponentId = c.Id
ORDER BY t.Id;
📊 4. FINAL OUTPUT (What you will see in DataGridView)
TransactionID | Username | Category    | Model            | Price
----------------------------------------------------------------
1             | John     | Processor   | Intel i5         | 12000
1             | John     | GPU         | RTX 3060         | 25000
1             | John     | RAM         | 16GB DDR4        | 5000
👉 IMPORTANT:

Same TransactionID = same PC build

Each row = 1 component

💻 5. C# CODE (Display in DataGridView)
string query = @"
SELECT 
    t.Id AS TransactionID,
    t.Username,
    t.ContactNumber,
    c.Category,
    c.Brand,
    c.Model,
    c.Price,
    t.DateCreated
FROM Transactions t
INNER JOIN TransactionDetails td ON t.Id = td.TransactionId
INNER JOIN Components c ON td.ComponentId = c.Id
ORDER BY t.Id";

using (SqlConnection conn = new SqlConnection(connStr))
{
    conn.Open();
    SqlDataAdapter da = new SqlDataAdapter(query, conn);
    DataTable dt = new DataTable();
    da.Fill(dt);

    dataGridView1.DataSource = dt;
}
🎯 BONUS (Better View - Grouped)
If you want 1 row per transaction, use this:

SELECT 
    t.Id,
    t.Username,
    t.TotalPrice,
    STRING_AGG(c.Model, ', ') AS Components
FROM Transactions t
JOIN TransactionDetails td ON t.Id = td.TransactionId
JOIN Components c ON td.ComponentId = c.Id
GROUP BY t.Id, t.Username, t.TotalPrice;
👉 Output:

Id | Username | Total | Components
------------------------------------------
1  | John     | 42000 | Intel i5, RTX 3060, 16GB
⚠️ FINAL ADVICE
✔ YES → Use 3 tables
✔ YES → Use JOIN
✔ YES → Show in 1 DataGridView

❌ DO NOT store everything in one column (bad practice)

🚀 If you want next
I can help you:

Save selected ComboBox items into TransactionDetails

Auto compute total price

Add search/filter in DataGridView

Just tell me 👍


string connStr = @"Data Source=.\SQLEXPRESS;Initial Catalog=PCBuilderDB;Integrated Security=True";

private void LoadComponents(string category, ComboBox cb)
{
    using (SqlConnection conn = new SqlConnection(connStr))
    {
        conn.Open();
        SqlCommand cmd = new SqlCommand("SELECT Model FROM Components WHERE Category=@cat", conn);
        cmd.Parameters.AddWithValue("@cat", category);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            cb.Items.Add(reader["Model"].ToString());
        }
    }
}

where should i put this in form1.cs?

Good question — placement matters here 👍

👉 You should put that code inside your BuilderForm (NOT MainForm/Form1) because that’s where your ComboBoxes (Motherboard, CPU, etc.) are.

📍 Where exactly to put it
✅ 1. At the TOP of your form (inside class)
Open BuilderForm.cs and put this:

using System.Data.SqlClient;
Then inside your form class:

public partial class BuilderForm : Form
{
    string connStr = @"Data Source=.\SQLEXPRESS;Initial Catalog=PCBuilderDB;Integrated Security=True";

    public BuilderForm()
    {
        InitializeComponent();
    }
✅ 2. Put your function BELOW constructor
private void LoadComponents(string category, ComboBox cb)
{
    using (SqlConnection conn = new SqlConnection(connStr))
    {
        conn.Open();
        SqlCommand cmd = new SqlCommand("SELECT Model FROM Components WHERE Category=@cat", conn);
        cmd.Parameters.AddWithValue("@cat", category);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            cb.Items.Add(reader["Model"].ToString());
        }
    }
}
✅ 3. Call it in Form Load Event
👉 Double-click the form to create Form_Load, then add:

private void BuilderForm_Load(object sender, EventArgs e)
{
    LoadComponents("Motherboard", cbMotherboard);
    LoadComponents("Processor", cbProcessor);
    LoadComponents("GPU", cbGPU);
    LoadComponents("RAM", cbRAM);
}
⚠️ VERY IMPORTANT (Common mistake)
Before loading, clear items:

cbMotherboard.Items.Clear();
Better version:

private void LoadComponents(string category, ComboBox cb)
{
    cb.Items.Clear(); // prevent duplicates

    using (SqlConnection conn = new SqlConnection(connStr))
    {
        conn.Open();
        SqlCommand cmd = new SqlCommand("SELECT Model FROM Components WHERE Category=@cat", conn);
        cmd.Parameters.AddWithValue("@cat", category);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            cb.Items.Add(reader["Model"].ToString());
        }
    }
}
🧠 Summary
✔ Put connection string → inside BuilderForm class
✔ Put LoadComponents method → inside BuilderForm class
✔ Call it → inside BuilderForm_Load

🚀 If you want next
I can help you:

Show Price when selecting item

Save selected parts into database

Auto compute total

Just tell me 👍



