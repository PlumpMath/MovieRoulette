using System;
using System.Collections.Generic;
using System.Configuration;
using System.Data;
using System.Linq;
using System.Windows;
using System.Diagnostics;
using System.Windows.Controls;
using System.Windows.Media;
using System.Xml;
using System.IO;
using Microsoft.Win32.SafeHandles;
using System.Windows.Threading;

namespace MovieRoulette
{
    /// <summary>
    /// Interaction logic for App.xaml
    /// </summary>
    public partial class App : Application
    {
        public void Application_Startup(object sender, StartupEventArgs e)
        {
            if (!Directory.Exists(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)+"\\MovieRoulette"))
                Directory.CreateDirectory(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)+"\\MovieRoulette");

            FileStream file = File.Open(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData) + "\\MovieRoulette\\settings.xml", FileMode.OpenOrCreate, FileAccess.Read);
            XmlDocument settings = new XmlDocument();
            try
            {
                settings.Load(file);
            }
            catch (XmlException xml)
            {
                settings.LoadXml("<?xml version=\"1.0\" encoding=\"ISO-8859-1\"?><main></main>");
            }

            if (settings.SelectSingleNode("/main").HasChildNodes)
            {
                MovieRoulette.Properties.Settings.Default.Player = settings.SelectSingleNode("//Player").InnerText;
                MovieRoulette.Properties.Settings.Default.VLCLanLocation = settings.SelectSingleNode("//VLCLanLocation").InnerText;
                MovieRoulette.Properties.Settings.Default.WMPLocation = settings.SelectSingleNode("//WMPLocation").InnerText;
                MovieRoulette.Properties.Settings.Default.DefaultLocation = settings.SelectSingleNode("//DefaultLocation").InnerText;
                file.Close();
            }
            else
            {
                file.Close();

                MovieRoulette.Properties.Settings.Default.DefaultLocation = Environment.GetFolderPath(Environment.SpecialFolder.MyVideos);

                XmlWriter write = XmlWriter.Create(File.Open(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData) + "\\MovieRoulette\\settings.xml", FileMode.OpenOrCreate, FileAccess.Write));
                write.WriteStartDocument();
                write.WriteWhitespace("\n");

                write.WriteStartElement("main");
                write.WriteWhitespace("\n");
                foreach (SettingsPropertyValue setting in MovieRoulette.Properties.Settings.Default.PropertyValues)
                {
                    write.WriteStartElement(setting.Name);
                    write.WriteValue(setting.PropertyValue.ToString());
                    write.WriteEndElement();
                    write.WriteWhitespace("\n");
                }
                write.WriteEndDocument();

                write.Close();
            }
        }

        void App_Exit(object sender, ExitEventArgs e)
        {
            XmlDocument settings = new XmlDocument();
            settings.Load(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData) + "\\MovieRoulette\\settings.xml");

            settings.SelectSingleNode("//Player").InnerText = MovieRoulette.Properties.Settings.Default.Player;
            settings.SelectSingleNode("//VLCLanLocation").InnerText = MovieRoulette.Properties.Settings.Default.VLCLanLocation;
            settings.SelectSingleNode("//WMPLocation").InnerText = MovieRoulette.Properties.Settings.Default.WMPLocation;
            settings.SelectSingleNode("//DefaultLocation").InnerText = MovieRoulette.Properties.Settings.Default.DefaultLocation;

            settings.Save(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData) + "\\MovieRoulette\\settings.xml");
        }

        public void HoverHandler(object sender, RoutedEventArgs e)
        {
            Control ctrl = (Control)sender;
            ctrl.Background = Brushes.PapayaWhip;
        }

        public void HoverOut(object sender, RoutedEventArgs e)
        {
            Control ctrl = (Control)sender;
            ctrl.Background = (Brush)(new BrushConverter()).ConvertFrom("#999999");
        }

        public void DispatcherUnhandledException_Handle(object sender, DispatcherUnhandledExceptionEventArgs e)
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

            string error = @"\n======================\nDate/Time: " + DateTime.Now + "\n======================\nSource: " + e.Exception.Source + "\nMethod: " + e.Exception.TargetSite +
                "\nMessage: " + e.Exception.Message + "\nStack Trace: " + e.Exception.StackTrace +
                "\nException: " + e.Exception.InnerException;
            byte[] message = (new System.Text.UTF8Encoding()).GetBytes(error);
            errorFile.Write(message, 0, message.Length);
        }
    }
}
