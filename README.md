# ST10278170-POE-PART-2-

Below is the Main Window Code to Start the Application for the Monthly System at Rosebank College. 
For Lecturers.


using System;
using System.Data;
using System.Data.SqlClient;
using System.Windows;
using System.Windows.Controls;

namespace PROG_PART2_POE_ST10278170
{
    public partial class MainWindow : Window
    {
        private string connectionString = "Server=darshan\\sqlexpress;Database=PROG_POE_PART2_ST10278170;Trusted_Connection=True;";

        public MainWindow()
        {
            InitializeComponent();
        }

        private void Button_Click(object sender, RoutedEventArgs e)
        {
            string name = UsernameTextBox.Text;
            string password = PasswordBox.Password;

            if (IsLecturer(name, password))
            {
                LecturerClaimPrompt lecturerView = new LecturerClaimPrompt();
                lecturerView.Show();
                this.Hide();
            }
            else if (IsAcademicManagerOrCoordinator(name, password))
            {
                Academic_Manager managerWindow = new Academic_Manager();
                managerWindow.Show();
                this.Hide();
            }
            else
            {
                MessageBox.Show("Invalid credentials. Please try again.");
            }
        }

        private bool IsLecturer(string LecturerName, string AccPassword)
        {
            string query = "SELECT COUNT(*) FROM Lecturer WHERE LecturerName = @LecturerName AND AccPassword = @AccPassword";

            using (SqlConnection conn = new SqlConnection(connectionString))
            {
                SqlCommand cmd = new SqlCommand(query, conn);

                cmd.Parameters.AddWithValue("@LecturerName", LecturerName);
                cmd.Parameters.AddWithValue("@AccPassword", AccPassword);

                conn.Open();
                int count = (int)cmd.ExecuteScalar();

                return count > 0;
            }
        }

        private bool IsAcademicManagerOrCoordinator(string name, string password)
        {
            string query = "SELECT COUNT(*) FROM Programme_Coordinator WHERE PcName = @name AND AccPassword = @password " +
                           "UNION ALL " +
                           "SELECT COUNT(*) FROM Academic_Manager WHERE AmName = @name AND AccPassword = @password";

            using (SqlConnection conn = new SqlConnection(connectionString))
            {
                SqlCommand cmd = new SqlCommand(query, conn);
                cmd.Parameters.AddWithValue("@name", name);
                cmd.Parameters.AddWithValue("@password", password);

                conn.Open();
                SqlDataReader reader = cmd.ExecuteReader();

                int count = 0;
                while (reader.Read())
                {
                    count += reader.GetInt32(0);
                }

                return count > 0;
            }
        }

        private void Button_Click2(object sender, RoutedEventArgs e)
        {

        }
    }
}
