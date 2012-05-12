using System;
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
using System.Windows.Shapes;
using System.Configuration;
using System.Windows.Forms;
using System.Diagnostics;

namespace MovieRoulette
{
    /// <summary>
    /// Interaction logic for Settings.xaml
    /// </summary>
    public partial class Settings : Window
    {
        public Settings()
        {
            this.WindowStyle = WindowStyle.None;
            InitializeComponent();
            ApplyButton.IsEnabled = false;
            
            LoadSettings();
        }

        private void RadioCheckedChanged(object sender, RoutedEventArgs e)
        {
            if (VideoLanOption.IsChecked.Value)
            {
                Properties.Settings.Default.Player = "VLC";
                VLCPlayerLocation.IsEnabled = true;
                VLCLocationBrowser.IsEnabled = true;

                WMPLocation.IsEnabled = false;
                WMPLocationBrowser.IsEnabled = false;
            }
            else
            {
                Properties.Settings.Default.Player = "WMP";
                VLCPlayerLocation.IsEnabled = false;
                VLCLocationBrowser.IsEnabled = false;

                WMPLocation.IsEnabled = true;
                WMPLocationBrowser.IsEnabled = true;
            }
            Anything_Changed(sender, e);
        }

        public void LoadSettings()
        {
            DefaultLocationTextBox.Text = Properties.Settings.Default.DefaultLocation;
            VLCPlayerLocation.Text = Properties.Settings.Default.VLCLanLocation;
            WMPLocation.Text = Properties.Settings.Default.WMPLocation;

            VideoLanOption.IsChecked = (Properties.Settings.Default.Player == "VLC");
            VLCPlayerLocation.IsEnabled = (Properties.Settings.Default.Player == "VLC");
            VLCLocationBrowser.IsEnabled = (Properties.Settings.Default.Player == "VLC");

            WMPOption.IsChecked = (Properties.Settings.Default.Player == "WMP");
            WMPLocation.IsEnabled = (Properties.Settings.Default.Player == "WMP");
            WMPLocationBrowser.IsEnabled = (Properties.Settings.Default.Player == "WMP");
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

        private void Close_Click(object sender, RoutedEventArgs e)
        {
            App.Current.Windows[1].Close();
        }

        private void Anything_Changed(object sender, EventArgs e)
        {
            if (!ApplyButton.IsEnabled)
                ApplyButton.IsEnabled = true;
        }

        private void ApplyChanges(object sender, RoutedEventArgs e)
        {
            Properties.Settings.Default.DefaultLocation = DefaultLocationTextBox.Text;
            if (VideoLanOption.IsChecked.Value)
            {
                Properties.Settings.Default.Player = "VLC";
                Properties.Settings.Default.VLCLanLocation = VLCPlayerLocation.Text;
            }
            else
            {
                Properties.Settings.Default.Player = "WMP";
                Properties.Settings.Default.WMPLocation = WMPLocation.Text;
            }

            ApplyButton.IsEnabled = false;
            App.Current.Windows[1].Close();
        }

        private void OpenFolderDialog_Click(object sender, RoutedEventArgs e)
        {
            string selectedFolder = string.Empty;

            FolderBrowserDialog selectFolderDialog = new FolderBrowserDialog();
            selectFolderDialog.Description = "Please select a directory: ";
            selectFolderDialog.ShowNewFolderButton = false;
            selectFolderDialog.RootFolder = Environment.SpecialFolder.MyComputer;
            if (selectFolderDialog.ShowDialog() == System.Windows.Forms.DialogResult.OK)
                DefaultLocationTextBox.Text = selectFolderDialog.SelectedPath;
        }

        private void OpenFileDialog_Click(object sender, RoutedEventArgs e)
        {
            System.Windows.Controls.Button button = (System.Windows.Controls.Button)sender;

            string selectedFile = string.Empty;

            OpenFileDialog dialog = new OpenFileDialog();
            dialog.Multiselect = false;
            if (button.CommandParameter.ToString() == "VLC")
            {
                dialog.Filter = "VLC|vlc.exe|Executables|*.exe";
                dialog.InitialDirectory = "C:\\Program Files (x86)\\VideoLAN\\VLC\\vlc.exe";
                dialog.Title = "VLC Location";
                if (dialog.ShowDialog() == System.Windows.Forms.DialogResult.OK)
                    VLCPlayerLocation.Text = dialog.FileName;

            }
            else if (button.CommandParameter.ToString() == "WMP")
            {
                dialog.Filter = "Windows Media Player|wmplayer.exe|Executables|*.exe";
                dialog.InitialDirectory = "C:\\Program Files\\Windows Media Player\\wmplayer.exe";
                dialog.Title = "WMP Location";

                if (dialog.ShowDialog() == System.Windows.Forms.DialogResult.OK)
                    WMPLocation.Text = dialog.FileName;
            }

        }
    }
}
