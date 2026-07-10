# The Rusty Spoon — Static Website on AWS

A fully responsive static website for **The Rusty Spoon**, a halaal cafe in Hillcrest, KwaZulu-Natal. Built as a school project to demonstrate AWS static website hosting and cloud migration benefits.

## Live Site

🔗 [View the website](http://YOUR-BUCKET-NAME.s3-website-REGION.amazonaws.com)

*(Replace with your actual S3 endpoint URL)*

## About the Project

The Rusty Spoon is a neighbourhood cafe experiencing operational challenges due to high volumes of bookings and orders being managed manually (paper records, phone calls, WhatsApp). This leads to order mix-ups, double bookings, and poor customer experience.

This project delivers a static website that allows customers to:
- Browse the full menu
- Book a table online
- Place orders for collection or delivery
- Receive instant confirmation

The site is hosted on **Amazon S3** as a static website, demonstrating a cost-effective cloud solution for small businesses.

## Features

- **Homepage** — Hero section with image slideshow, overview, gallery carousel, and menu preview
- **Menu page** — Full menu with categories (Bagels, Coffee, Matcha, Desserts, Mocktails) and ZAR pricing
- **Booking form** — Client-side validated form for table reservations
- **Order form** — Collapsible menu categories with quantity controls and live order total
- **Confirmation page** — Displays booking/order summary after submission
- **Responsive design** — Mobile-friendly with hamburger navigation
- **Image gallery** — Horizontal carousel with arrow navigation

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | HTML5, CSS3, Vanilla JavaScript |
| Hosting | Amazon S3 (Static Website Hosting) |
| Domain | S3 bucket website endpoint |

## Project Structure

```
├── index.html              # Homepage
├── menu.html               # Full menu
├── booking.html            # Booking form
├── order.html              # Order form
├── confirmation.html       # Confirmation page
├── css/
│   └── style.css           # All styles
├── js/
│   └── main.js             # Form validation, carousel, slideshow
├── images/
│   ├── menu-01.jpeg        # Gallery/slideshow images
│   ├── ...
│   └── menu-13.jpeg
└── README.md
```

## AWS Services

### Currently Implemented
- **Amazon S3** — Static website hosting and image storage

### Recommended for Future Enhancement
- **AWS Cognito** — User authentication (customer accounts)
- **Amazon DynamoDB** — Database for storing bookings and orders
- **AWS Lambda** — Serverless functions (process forms, send emails)
- **Amazon API Gateway** — REST API endpoints
- **Amazon SNS** — Notifications to staff for new orders

## Deployment

1. Create an S3 bucket with a unique name
2. Enable Static Website Hosting (index document: `index.html`)
3. Unblock public access and add a bucket policy for public read
4. Upload all project files maintaining the folder structure
5. Access the site via the S3 website endpoint URL

## Cost

This project runs entirely within the **AWS Free Tier**:
- S3: 5 GB storage, 20,000 GET requests/month
- Total monthly cost: **R0** for typical small business traffic

## Screenshots

*(Add screenshots of your live site here)*

## Author

**[Your Name]**

Built as part of a cloud computing module project — demonstrating AWS migration benefits for small businesses.

## License

This project is for educational purposes.
