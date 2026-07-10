# ☁️ The Rusty Spoon – Static Website Hosted on AWS

A fully responsive static website developed for **The Rusty Spoon**, a Halaal café located in Hillcrest, KwaZulu-Natal. This project was created as part of my cloud computing coursework to demonstrate how a small business can migrate from traditional hosting to a cloud-based solution using Amazon Web Services (AWS).

---

## 🌐 Live Website

🔗 **Website:**  
http://rustyspoon-nambi.s3-website-eu-west-1.amazonaws.com/index.html#gallery

---

# 📖 Project Overview

The Rusty Spoon is a neighbourhood café that currently manages customer bookings and food orders manually through phone calls, WhatsApp messages, and handwritten records. As the business grows, this process becomes inefficient and increases the risk of double bookings, misplaced orders, and slower customer service.

To address these challenges, I designed and developed a responsive static website that provides customers with an easy way to browse the menu, reserve tables, and place food orders online.

The website is hosted on **Amazon S3 Static Website Hosting**, providing a reliable, scalable, and cost-effective cloud solution for a small business.

---

# 🎯 Project Objectives

Through this project, I was able to:

- Design a responsive café website using HTML, CSS, and JavaScript.
- Host a static website on Amazon S3.
- Configure static website hosting.
- Create an online menu for customers.
- Develop a booking form with client-side validation.
- Build an interactive ordering system.
- Demonstrate the benefits of cloud migration for a small business.

---

# ✨ Features

## 🏠 Homepage

- Hero banner with automatic slideshow
- Café introduction
- Featured menu items
- Customer image gallery
- Responsive navigation menu

---

## 🍽️ Menu

Customers can browse a complete digital menu, including:

- Bagels
- Coffee
- Matcha
- Desserts
- Mocktails

All menu items display prices in South African Rand (ZAR).

---

## 📅 Table Booking

Customers can:

- Book a table online
- Enter reservation details
- Receive booking confirmation
- Validate required fields before submission

---

## 🛒 Online Ordering

The ordering page allows customers to:

- Browse food categories
- Select quantities
- View a running order total
- Submit their order for collection or delivery

---

## ✅ Confirmation Page

After submitting a booking or order, customers are redirected to a confirmation page displaying a summary of their submission.

---

## 📱 Responsive Design

The website is fully responsive and works across:

- Desktop
- Tablet
- Mobile devices

Features include:

- Mobile navigation
- Flexible layouts
- Responsive images

---

# 🛠️ Technologies Used

| Category | Technology |
|-----------|------------|
| Frontend | HTML5 |
| Styling | CSS3 |
| Programming | JavaScript (Vanilla JS) |
| Cloud Hosting | Amazon S3 |
| Deployment | AWS Static Website Hosting |

---

# ☁️ AWS Services Used

### Amazon S3

I used Amazon S3 to:

- Store all website files
- Host the static website
- Deliver website content over the internet
- Store images and other project assets

---

# 🚀 Deployment Process

To deploy the website, I completed the following steps:

1. Created an Amazon S3 bucket with a unique name.
2. Enabled Static Website Hosting.
3. Configured **index.html** as the default landing page.
4. Updated bucket permissions to allow public access.
5. Added a bucket policy for public read access.
6. Uploaded all project files while maintaining the folder structure.
7. Accessed the website using the S3 Website Endpoint URL.

---

# 📂 Project Structure

```
The-Rusty-Spoon/
│
├── index.html
├── menu.html
├── booking.html
├── order.html
├── confirmation.html
│
├── css/
│   └── style.css
│
├── js/
│   └── main.js
│
├── images/
│   ├── menu-01.jpeg
│   ├── menu-02.jpeg
│   ├── ...
│   └── menu-13.jpeg
│
└── README.md
```

---

# 📈 Future Improvements

If I continue developing this project, I would integrate additional AWS services to make the website fully dynamic.

Potential enhancements include:

- **Amazon DynamoDB** to store customer bookings and orders.
- **AWS Lambda** to process booking and order requests.
- **Amazon API Gateway** to expose secure REST APIs.
- **Amazon Cognito** for customer registration and login.
- **Amazon SNS** to notify staff when new orders or bookings are received.
- Online payment integration.

---

# 💡 Skills Demonstrated

This project allowed me to gain practical experience in:

- Responsive web design
- HTML5, CSS3, and JavaScript
- Amazon S3 Static Website Hosting
- Cloud deployment
- AWS storage services
- Website structure and navigation
- Client-side form validation
- Cloud migration concepts
- Git and GitHub project documentation

---

# 📸 Screenshots

Include screenshots of:

- Homepage
- Menu page
- Booking page
- Order page
- Confirmation page

---

# 📚 Key Takeaways

This project helped me understand how Amazon S3 can be used to host static websites quickly and cost-effectively. I also gained practical experience in deploying web applications to the cloud and learned how cloud services can improve business operations by providing scalable, reliable, and low-maintenance hosting solutions.

---

# 👨‍💻 Author

**Inga Mondliwa**

Cloud Computing Student | AWS Cloud Practitioner | Linux & AWS Enthusiast

---

# 📄 License

This project was developed for educational purposes as part of a cloud computing module.
