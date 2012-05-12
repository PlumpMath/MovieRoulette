using System;
using System.Windows.Forms;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Data;
using System.Windows.Documents;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using System.Windows.Navigation;
using System.Windows.Shapes;
using System.IO;
using System.Diagnostics;
using System.Windows.Interop;
using System.Configuration;

namespace MovieRoulette
{
    /// <summary>
    /// Interaction logic for MainWindow.xaml
    /// </summary>
    public partial class MainWindow : Window
    {
        public List<string> files;
        public Dictionary<string, string> directories;

        public MainWindow()
        {
            this.WindowStyle = WindowStyle.None;
            InitializeComponent();
            FolderName.Text = Properties.Settings.Default.DefaultLocation;
        }

        private void OpenFolderDialog_Click(object sender, RoutedEventArgs e)
        {
            string selectedFolder = string.Empty;

            FolderBrowserDialog selectFolderDialog = new FolderBrowserDialog();
            selectFolderDialog.Description = "Please select a directory: ";
            selectFolderDialog.ShowNewFolderButton = false;
            selectFolderDialog.RootFolder = Environment.SpecialFolder.MyComputer;
            if (selectFolderDialog.ShowDialog() == System.Windows.Forms.DialogResult.OK)
            {
                selectedFolder = selectFolderDialog.SelectedPath;
                FolderName.Text = selectedFolder;
            }
        }

        private void GenerateFolders()
        {
            Mouse.OverrideCursor = System.Windows.Input.Cursors.Wait;
            if (FolderName.Text != String.Empty)
            {
                try
                {
                    Directory.SetCurrentDirectory(FolderName.Text);
                    directories = new Dictionary<string, string>();

                    directories.Add(Directory.GetCurrentDirectory(), "..");
                    RecursiveDirectoryScan(Directory.GetCurrentDirectory(), ref directories);

                    FoldersListBox.ItemsSource = directories.Values;
                    FoldersListBox.Items.Refresh();
                    GenerateFiles();
                }
                catch (UnauthorizedAccessException f)
                {
                    Console.WriteLine(f.Message);
                }
            }
            Mouse.OverrideCursor = System.Windows.Input.Cursors.Arrow;
        }

        private void GenerateFiles()
        {
            try
            {
                files = new List<string>();
                foreach (string dir in directories.Keys)
                {
                    VideoScan(dir, ref files);
                }

                for (int i = 0; i < files.Count; i++)
                {
                    files[i] = files[i].Replace(Directory.GetCurrentDirectory(), "..");
                }
                FileListBox.ItemsSource = files;
                FileListBox.Items.Refresh();
            }
            catch (DirectoryNotFoundException f)
            {
                Console.WriteLine(f.ToString());
            }
        }

        protected void RecursiveDirectoryScan(string directory, ref Dictionary<string, string> directories)
        {
            try
            {
                if (Directory.GetDirectories(directory).Count() == 0)
                    return;
                else
                {
                    foreach (string dir in Directory.GetDirectories(directory))
                    {
                        directories.Add(dir, dir.Replace(Directory.GetCurrentDirectory(), ".."));
                        RecursiveDirectoryScan(dir, ref directories);
                    }
                }
            }
            catch (UnauthorizedAccessException e)
            {
                Console.WriteLine(e.ToString());
            }
        }

        protected void VideoScan(string directory, ref List<string> files)
        {
            List<string> fileExtensions = new List<string>() { ".avi", ".mpg", ".mp4", ".mpeg", ".divx", ".flv", ".mkv", ".mov", ".mpeg4", ".ts", ".vob", ".m4a", ".wmv", ".wma" };

            foreach (string file in Directory.GetFiles(directory))
            {
                FileInfo fi = new FileInfo(file);

                if (fileExtensions.Contains(fi.Extension))
                    files.Add(file);
            }
        }

        private void RemoveSelectedFile_Click(object sender, RoutedEventArgs e)
        {
            if (FileListBox.SelectedItems.Count > 0)
            {
                foreach (string item in FileListBox.SelectedItems)
                {
                    files.Remove(item);
                }

                FileListBox.ItemsSource = files;
                FileListBox.Items.Refresh();
            }            
        }

        private void RemoveSelectedFolder_Click(object sender, RoutedEventArgs e)
        {
            if (FoldersListBox.SelectedItems.Count > 0)
            {
                foreach (string item in FoldersListBox.SelectedItems)
                {
                    directories.Remove(item.Replace("..", Directory.GetCurrentDirectory()));
                }

                FoldersListBox.ItemsSource = directories.Values;
                FoldersListBox.Items.Refresh();
                GenerateFiles();
            }
        }

        private void AddFolder_Click(object sender, RoutedEventArgs e)
        {
            string selectedFolder = string.Empty;

            FolderBrowserDialog selectFolderDialog = new FolderBrowserDialog();
            selectFolderDialog.Description = "Please select a directory: ";
            selectFolderDialog.ShowNewFolderButton = false;
            selectFolderDialog.RootFolder = Environment.SpecialFolder.MyComputer;
            if (selectFolderDialog.ShowDialog() == System.Windows.Forms.DialogResult.OK)
            {
                selectedFolder = selectFolderDialog.SelectedPath;

                RecursiveDirectoryScan(selectedFolder, ref directories);

                FoldersListBox.ItemsSource = directories.Values;
                FoldersListBox.Items.Refresh();
                GenerateFiles();
            }
        }

        private void WatchMovie_Click(object sender, RoutedEventArgs e)
        {
            Random num = new Random();
            RunVLC(files[num.Next(files.Count)].Replace("..", Directory.GetCurrentDirectory()), Properties.Settings.Default.Player);
        }

        public static int RunVLC(string fileLocation, string player)
        {
            int ExitCode;
            ProcessStartInfo ProcessInfo;
            Process Process = new Process();

            if (player == "VLC")
                ProcessInfo = new ProcessStartInfo(Properties.Settings.Default.VLCLanLocation, "-vvv \"" + fileLocation + "\" --fullscreen");
            else
                ProcessInfo = new ProcessStartInfo(Properties.Settings.Default.WMPLocation, "\"" + fileLocation + "\" /fullscreen");
            ProcessInfo.CreateNoWindow = true;
            ProcessInfo.UseShellExecute = false;
            try
            {
                Process = Process.Start(ProcessInfo);
            }
            catch (Exception e)
            {
                System.Windows.MessageBox.Show("There was an error running the video player, please check your settings and try again");
                WriteError(e);
            }
            Process.WaitForExit();
            ExitCode = Process.ExitCode;
            Process.Close();

            return ExitCode;
        }

        private void FolderName_TextChanged(object sender, TextChangedEventArgs e)
        {
            GenerateFolders();
        }

        public void RefreshButton_Click(object sender, RoutedEventArgs e)
        {
            GenerateFolders();
        }

        private void Close_Click(object sender, RoutedEventArgs e)
        {
            foreach (Window window in App.Current.Windows)
                window.Close();
        }
        
        private void Grid_MouseLeftButtonDown(object sender, MouseButtonEventArgs e)
        {
            try
            {
                this.DragMove();
            }
            catch (InvalidOperationException f)
            {
                Console.WriteLine(f.Message);
            }
        }

        private void SettingsButton_Click(object sender, RoutedEventArgs e)
        {
            if (App.Current.Windows.Count == 1)
            {
                Settings newWindow = new Settings();
                newWindow.Show();
            }
            else
            {
                App.Current.Windows[1].Focus();
            }
        }

        private static void WriteError(Exception e)
        {
            FileStream errorFile;
            try
            {
                errorFile = File.OpenWrite(Environment.SpecialFolder.LocalApplicationData + "\\MovieRoulette\\errorLog.txt");
            }
            catch (FileNotFoundException fnfe)
            {
                File.CreateText(Environment.SpecialFolder.LocalApplicationData + "\\MovieRoulette\\errorLog.txt");
                errorFile = File.OpenWrite(Environment.SpecialFolder.LocalApplicationData + "\\MovieRoulette\\errorLog.txt");
            }

            string error = @"\n======================\nDate/Time: " + DateTime.Now + "\n======================\nSource: " + e.Source + "\nMethod: " + e.TargetSite +
                "\nMessage: " + e.Message + "\nStack Trace: " + e.StackTrace +
                "\nException: " + e.InnerException;
            byte[] message = (new System.Text.UTF8Encoding()).GetBytes(error);
            errorFile.Write(message, 0, message.Length);
        }
    }
}
