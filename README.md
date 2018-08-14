using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Web;

namespace ExtractMethodsDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            #region vars
            // string filePath = "C:/Users/228757/Desktop/temp Location";
            bool ToBeCommented = false;//true :if within Left Curly Braces
            bool PostPublicMember = false;
            bool testing = false;
            int NumOfFiles = 0;
            int StackCounter = 0;
            int StartLineNumber = 0;
            int NumOfLeftCurlyBrac = 0;
            string filePath = "D:/tempdata";
            string currentLine = "";
            List<string> OutPutFile = new List<string>();
            #endregion

            #region Automated Process
            try
            {
                if (testing == false)
                {

                    if (Directory.Exists(filePath))
                    {
                        #region Get File Content
                        NumOfFiles = GetFileContent(filePath);
                        if (NumOfFiles > new int())// Destination has file(s)
                        {
                            #region read all  files in Destintions  
                            for (int i = 0; i < NumOfFiles; i++)
                            {
                                var files = Directory.GetFiles(filePath);
                                string fileName = Path.GetFileName(files[i]).Replace(".cs", "");
                                string[] fileContent = File.ReadAllLines(files[i]);
                                string WriteFileName = "/Writing Destination" + "/" + (fileName + "-__Updated__ver" + DateTime.Now.Millisecond.ToString()).Trim() + ".cs";//Assigning Random Number to new file
                                #region Processing File
                                for (int j = 0; j < fileContent.Length; j++)
                                {
                                    if (!ToBeCommented)
                                    {
                                        #region Normal Flow 
                                        if (IsPrivateMember(fileContent[j]))
                                        {
                                            #region Handeling Private Members
                                            currentLine = CommentPrivateMember(fileContent[j]);
                                            OutPutFile.Add(currentLine);
                                            #endregion
                                        }

                                        #region Handeling Public Members
                                        else if (IsPublicMemberLine(fileContent[j]))
                                        {

                                            OutPutFile.Add(fileContent[j] + " ;");
                                       
                                        }

                                        else if (HasLeftCurlyBraces(fileContent[j]) && IsPublicMemberLine(fileContent[j-1]))
                                        {
                                            OutPutFile.Add(CommentLine(fileContent[j]));
                                            NumOfLeftCurlyBrac += 1;
                                            ToBeCommented = true;
                                        }
                                        

                                        #endregion
                                        else
                                        {
                                            OutPutFile.Add(fileContent[j]);
                                        }
                                        #endregion
                                    }
                                    else // ToBeCommented == True
                                    {
                                        if (HasLeftCurlyBraces(fileContent[j]))
                                        {
                                            OutPutFile.Add(CommentLine(fileContent[j]));
                                            NumOfLeftCurlyBrac += 1;
                                            ToBeCommented = true;
                                        }
                                         if (HasRightCurlyBraces(fileContent[j]))
                                        {
                                            OutPutFile.Add(CommentLine(fileContent[j]));
                                            NumOfLeftCurlyBrac -= 1;
                                        

                                        }
                                        if (NumOfLeftCurlyBrac == 0)
                                        {
                                            ToBeCommented = false;
                                        }
                                        else
                                        { 
                                        #region Within Left Curly Braces
                                        OutPutFile.Add(CommentLine(fileContent[j]));
                                            #endregion
                                        }
                                    }


                                }


                                #endregion

                                #region Write into New File
                                #region Using StreamWriter
                                //using (StreamWriter writetext = new StreamWriter(filePath   + WriteFileName))//update with file name
                                //{
                                //writetext.WriteLine(fileContent);
                                //} 
                                #endregion
                                File.WriteAllLines(filePath + WriteFileName, OutPutFile.ToArray());
                                #endregion

                            }
                            #endregion

                        }
                        else
                        {
                            Console.WriteLine("Destination has no files");
                        }


                        #endregion

                    }
                    else
                    {
                        Console.WriteLine("Destination Does not Exsist");
                    }

                }
            }
            catch (Exception ex)
            {

                Console.WriteLine("Exception occured " + ex.Message);
            }
            #endregion
            #region Testing
            #region OLD Public Member Code
            //    if (fileContent[i].EndsWith("{"))
            //    {
            //        string temp = fileContent[j].Substring(0, fileContent[j].Length - 2) + " ; " + " {";
            //        OutPutFile.Add(temp + " ;");
            //    }
            //    else
            //    {
            //        OutPutFile.Add(fileContent[j] + ";");
            //    }
            //}
            //else if (IsSetterOrGetter(fileContent[j]))
            //{
            //    OutPutFile.Add(CommentLine(fileContent[j]));
            //}  

            //else if (HasPublicKeyword(fileContent[j]))
            //                        {
            //                            #region Handeling MultiLine Get&Set
            //                            if (HasBrace(fileContent[j + 1]))
            //                            {
            //                                //if (HasGetOrSet(j + 2))
            //                                //{

            //                                //}
            //                                OutPutFile.Add(fileContent[j]);
            //                            }




            //if (HasLeftCurlyBraces(fileContent[j]))
            //{
            //    ToBeCommented = true;
            //}
            //else if (HasRightCurlyBraces(fileContent[j]))
            //{
            //    OutPutFile.Add(CommentLine(fileContent[j]));
            //    StackCounter -= 1;
            //}


            //else
            //{
            //    #region Within Left Curly Braces
            //    OutPutFile.Add(CommentLine(fileContent[j])); 
            //  ToBeCommented = true;
            //    #endregion
            //}
            #endregion



            #endregion

            #region OLD Work
            #region GetMethods Names
            //var methodsNames = GetAllMethodNames(fileName);
            //foreach (var item in methodsNames)
            //{
            //    Console.WriteLine(item);
            //}
            #endregion
            #region Comment Private Members
            //  var output = CommentPrivateMembers(fileName);
            #endregion
            #region RemoveGetandSetFromPublicMembers
            // var output = RemoveGetandSetFromPublicMembers(filePath);
            #endregion
            //foreach (var item in output)
            //{
            //    Console.WriteLine(item);
            //}
            #endregion
            Console.WriteLine("Process Compeleted SucessFully");
            Console.Read();

        }


        #region Methods
        public static int GetFileContent(string fileName)
        {

            var files = Directory.GetFiles(fileName);
            int NumOfFiles = files.Count();
            //var fileContent = File.ReadAllLines(files[0]);
            return NumOfFiles;

        }

        public static List<string> RemoveGetandSetFromPublicMembers(string fileName)
        {
            List<string> publicMembersEdited = new List<string>();
            var files = Directory.GetFiles(fileName);
            var strPublicMemberLines = File.ReadAllLines(files[2])
                                        .Where(a => a.Contains("public") && !a.Contains("_")
                                        && !a.Contains("class") && !a.Contains("()")
                                        || a.Contains("get") || a.Contains("set")

                                              );




            //not a constructor or a class
            foreach (var item in strPublicMemberLines)
            {
                if (item.IndexOf("{") != -1)
                {
                    int index1 = item.IndexOf("{");
                    int index2 = item.LastIndexOf(";");
                    string pattern1 = " ; /* ";

                    string temp = "";
                    temp = item.Substring(0, item.IndexOf("{") - 1) + pattern1;
                    temp = temp + item.Substring(index1, item.Length - index1) + " */";

                    //temp.Insert(temp.IndexOf("{") - 1, );
                    //temp.Insert(temp.IndexOf("}") + 1, "*/");
                    publicMembersEdited.Add(temp);
                }
            }
            return publicMembersEdited.ToList();
        }
        public static List<string> CommentPrivateMembers(string fileName)
        {
            List<string> methodNames = new List<string>();
            var files = Directory.GetFiles(fileName);
            var strMethodLines = File.ReadAllLines(files[2])
                                        .Where(a => a.Contains("private") &&
                                                    !a.Contains("_") && !a.Contains("class"));//not a constructor

            foreach (var item in strMethodLines)
            {
                if (item.IndexOf(";") != -1)
                {
                    string temp = "/*" + item + "   */";
                    methodNames.Add(temp);
                }
            }
            return methodNames.Distinct().ToList();
        }

        public static bool IsPrivateMember(string line)
        {
            return line.ToLower().Contains("private")
                                         && !line.Contains("_")
                                         && !line.Contains("class");


        }

        public static string CommentPrivateMember(string line)
        {
            string commentedString = "";
            if (line.IndexOf(";") != -1)
            {
                commentedString = "/*" + line + "   */";

            }
            return commentedString;

        }

        public static bool IsPublicMemberLine(string line)
        {
            return line.ToLower().Contains("public")
                                         && !line.Contains("_")
                                         && !line.Contains("class")
                                         && !line.Contains("enum");
            




        }
        public static bool HasLeftCurlyBraces(string line)
        {
            return line.ToLower().Contains("{");

        }
        public static bool HasRightCurlyBraces(string line)
        {
            return line.ToLower().Contains("}");

        }
        public static bool HasBothCurlyBraces(string line)
        {
            return line.ToLower().Contains("{") && line.ToLower().Contains("}");

        }


        public static string ProcessPublicMember(string line)
        {
            string newString = "";
            if (line.IndexOf("{") != -1)
            {
                int index1 = line.IndexOf("{");
                int index2 = line.LastIndexOf(";");
                string pattern1 = " ; /* ";


                newString = line.Substring(0, line.IndexOf("{") - 1) + pattern1;
                newString = newString + line.Substring(index1, line.Length - index1) + " */";

                //temp.Insert(temp.IndexOf("{") - 1, );
                //temp.Insert(temp.IndexOf("}") + 1, "*/");


            }
            return newString;
        }
        public static bool HasSetterOrGetter(string line)
        {
            return line.ToLower().Contains("set")
                                 || line.Contains("get")
                                 || line.Contains("return");


        }
        public static string CommentLine(string line)
        {
            return " /* " + line + " */ ";
        }




        #endregion
    }
}
