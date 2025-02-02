# books-library-sql-project
create database onlinebookstore;
use onlinebookstore;

CREATE TABLE books (
    Book_ID INT PRIMARY KEY,
    Title VARCHAR(100),
    Author VARCHAR(100),
    Genre VARCHAR(50),
    Published_Year INT,
    Price INT,
    Stock INT
);

create table customers(
Customer_ID int primary key,
Name varchar(50),
Email varchar(50),
Phone varchar(20),
City varchar(50),
Country varchar(50)
);

create table orders(
Order_ID int primary key,
Customer_ID	int references customers(customer_id),
Book_ID	int references books(book_id),
Order_Date date,
Quantity int,
Total_Amount int
);

**-- 1) Retrieve all books in the "Fiction" genre:**
select * from books
where genre = "fiction"

**-- 2) Find books published after the year 1950:**
select * from books
where published_year > 1950;

**-- 3) List all customers from the Canada:**
select * from customers
where country = "canada"

**-- 4) Show orders placed in 2023 November:**
select * from orders
where order_date between '2023-11-01' and '2023-11-30'

-- 5) Retrieve the total stock of books available:
select count(stock) from books

-- 6) Find the details of the most expensive book:
select * from books
order by price desc
limit 1

or we can use sub query

select * from books
where price = (select max(price) from books)
limit 1


-- 7) Show all customers who ordered more than 1 quantity of a book:
select * from orders
where quantity > 1

-- 8) Retrieve all orders where the total amount exceeds $20
select * from orders
where total_amount > 20

-- 9) List all genres available in the Books table:
select distinct(genre) from books

-- 10) Find the book with the lowest stock:
select * from books
order by stock
limit 1


-- 11) Calculate the total revenue generated from all orders:
select sum(total_amount) as revenue from orders


Advance Query
-- 1) Retrieve the total number of books sold for each genre:
select sum(quantity),book.genre from orders
join books on
orders.book_id = books.book_id
group by genre

-- 2) Find the average price of books in the "Fantasy" genre:
select avg(price) from books
where genre = "fantasy"

-- 3) List customers who have placed at least 2 orders:
select name,count(order_id) as total_order,orders.customer_id from orders
join customers on
orders.customer_id = customers.customer_id
group by customer_id,name
having count(order_id) >= 2

-- 4) Find the most frequently ordered book:
select books.title,orders.book_id,count(order_id) as total_order
from orders join
books on
orders.book_id = books.book_id
group by book_id,title
order by total_order desc
limit 1;

-- 5) Show the top 3 most expensive books of 'Fantasy' Genre :
select * from books
where genre = "fantasy"
order by price desc
limit 3;

-- 6) Retrieve the total quantity of books sold by each author:
select books.author,sum(quantity) as total_quantity from orders
join books on
orders.book_id = books.book_id
group by author;

-- 7) List the cities where customers who spent over $30 are located:
select distinct city,total_amount from orders
join customers on
orders.customer_id = customers.customer_id
where total_amount > 30

-- 8) Find the customer who spent the most on orders:
select customers.name,sum(total_amount) from orders join customers on
orders.customer_id = customers.customer_id
group by customers.name
order by sum(total_amount) desc
limit 1

--9) Calculate the stock remaining after fulfilling all orders:
select books.book_id,title,stock,coalesce(sum(quantity),0) as order_quantity,stock-coalesce(sum(quantity),0) as remaining_stock
from 
books left join orders on
books.book_id = orders.book_id
group by book_id

