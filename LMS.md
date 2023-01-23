# Library-Management-System
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace LMS
{
    class Program
    {
        public class Student
        {
            public int Id { get; set; }
            public string Student_Name { get; set; }
            public string Address { get; set; }
            public int Age { get; set; }
            public string Email { get; set; }
            public string Password { get; set; }
            public int Number_of_books { get; set; }

            public string connStr = "Data Source=DESKTOP-TL8MHN8;Initial Catalog=LMS;Integrated Security=True";

            public int Return_book(Student std)
            {
                SqlConnection conn = new SqlConnection(connStr);
                conn.Open();

                Student student = new Student();
                int student_id = student.GetStudentId(std);
                using (SqlCommand cmdSelect = new SqlCommand("Select * from Book where User_Id = @id", conn))
                {
                    cmdSelect.Parameters.AddWithValue("id", student_id);
                    using (SqlDataReader reader = cmdSelect.ExecuteReader())
                    {
                        Console.WriteLine("{0,-15} {1,-15} ", "ID", "Title");
                        while (reader.Read())
                        {
                            Console.WriteLine("{0,-15} {1,-15}", reader["Id"].ToString(), (string)reader["Title"]);
                        }
                    }
                }
                Console.WriteLine("Select the book You are returning: ");
                int book = Convert.ToInt32(Console.ReadLine());
                using (SqlCommand cmdUpdate = new SqlCommand("UPDATE Book SET Quantity = Quantity + 1, User_Id = @userId WHERE Id = @id", conn))
                {
                    cmdUpdate.Parameters.AddWithValue("@id", book);
                    cmdUpdate.Parameters.AddWithValue("@userId", 0);
                    cmdUpdate.ExecuteNonQuery();
                }
                using (SqlCommand cmdUpdateStudent = new SqlCommand("UPDATE Student SET Num_of_books = Num_of_books - 1 WHERE  ID = @id", conn))
                {
                    cmdUpdateStudent.Parameters.AddWithValue("@id", student_id);
                    return cmdUpdateStudent.ExecuteNonQuery();
                }

            }
