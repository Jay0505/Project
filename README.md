using System;
using System.Collections;
using System.Collections.Generic;
using System.Text;

namespace project_assignment3
{

	class Node
	{
		private string Data;
		private Node First;
		private Node Next;
		private Node Parent;

		public string pdata
		{
			get
			{
				return Data;
			}
			set
			{
				Data = value;
			}
		}

		public Node pfirst
		{
			get
			{
				return First;
			}

			set
			{
				First = value;
			}
		}

		public Node pnext
		{
			get
			{
				return Next;
			}
			set
			{
				Next = value;
			}
		}

		public Node pparent
		{
			get
			{
				return Parent;
			}
			set
			{
				Parent = value;
			}
		}

		public Node()
		{
			pdata = string.Empty;
			pfirst = null;
			pnext = null;
			pparent = null;
		}

		public Node(string data)
		{
			pdata = data;
			pfirst = null;
			pnext = null;
			pparent = null;
		}
		public Node(string data, Node firstt, Node next, Node parent)
		{
			Data = data;
			First = firstt;
			Next = next;
			Parent = parent;
		}
	}

//------------------------------------------------------------------------------------------------------------------------------------------------//
//------------------------------------------------------------------------------------------------------------------------------------------------//

	class MainClass
	{
		public static Stack<char> operator_st = new Stack<char>();
		public static Stack<string> operand_st = new Stack<string>();
		public static Stack<string> bracket_st = new Stack<string>();
		public static Stack<Node> node_st = new Stack<Node>();
		public static Stack<Node> node_der = new Stack<Node>();
		public static Stack<Node> derivative_result = new Stack<Node>();
		public static Stack<Node> brackets_encountered = new Stack<Node>();
		public static Stack<Node> temp_stack = new Stack<Node>();
		public static Stack<char> temp_operator_st = new Stack<char>();
		public static Stack<int> interger_st = new Stack<int>();
		public static Stack<Node> operand_node = new Stack<Node>();
		public static int globalint;
		public static char globalchar;



		public static void Main(string[] args)
		{
			string str;
			DateTime dt2=DateTime.Now;
			Console.WriteLine("\nEnter your regular expression");
			string stregex = Console.ReadLine();

			string original_regex = String_Checker_fucntion(stregex);

			if (original_regex == stregex)
			{
				if (original_regex == "(a+a*)+b" || original_regex == "(a*a*)*" || original_regex == "(a*)*")
				{
					Console.WriteLine("Enter how many a's you want to add?");
					int n = Convert.ToInt32(Console.ReadLine());
					str = SB('a', n);
					//Console.WriteLine(str);
				}
				else
				{
					Console.WriteLine("\nEnter your string");
					str = Console.ReadLine();
				}
			}
			else
			{
				str = SB(globalchar,globalint);
				Console.WriteLine("Character is - {0}", globalchar);
				Console.WriteLine("Number of {0}'s in the string are {1}", globalchar, globalint);
			}
			Console.WriteLine(original_regex);
			Console.WriteLine("------------------------------------------------");
			Console.WriteLine(str);
			DateTime dt1 = DateTime.Now;
			Node root = ConstructTree(original_regex);
			if (root == null)
				Console.WriteLine("\nPlease enter valid regular expression");

			else
			{
				Console.WriteLine("--------------------------------------------------------");
				Console.WriteLine("Contents of the regular expression expression tree are :");
				//Display_Contents_of_the_node(root);
				Console.WriteLine(Nullable(root));
			}

			Node deri_node = new Node();

			for (int i = 0; i <= str.Length - 1; i++)
			{
				char alp = str[i];
				if (i == 0)
				{
					derivative_result.Push(root);
					deri_node = Derivative(root, str[i]);
				}

				else
				{
					derivative_result.Push(deri_node);
					deri_node = Derivative(deri_node, str[i]);
				}

			}

			Console.WriteLine("--------------------------------------------------------");
			Console.WriteLine("The contents of derinode are : ");
			//Display_Contents_of_the_node(deri_node);
			bool IsDerivativeNullable = Nullable(deri_node);
			Console.WriteLine("Is the deri node nullable? : {0} ", IsDerivativeNullable);
			Console.WriteLine("Number of contents in der stack is {0}", derivative_result.Count);
			if (IsDerivativeNullable)
			{
				Node mkeps_node = mkeps(deri_node);
				//derivative_result.Pop();
				Console.WriteLine("----------------------------------------------------");
				Console.WriteLine("mkeps result : ");
				//Display_Contents_of_mkeps_node(mkeps_node);

				Node inject_result = new Node();
				for (int i = str.Length - 1; i >= 0; i--)
				{
					Node deri_node2 = derivative_result.Pop();
					if (i == str.Length - 1)
						inject_result = Inject(deri_node2, str[i], mkeps_node);

					else
						inject_result = Inject(deri_node2, str[i], inject_result);
				}
				dt2 = DateTime.Now;
				Console.WriteLine("----------------------------------------------------");
				Console.WriteLine("Inject result: ");
				if (inject_result.pdata != null)
				{
					Console.WriteLine("matched");
				}
				Display_Contents_of_mkeps_node(inject_result);
			}
			if (derivative_result.Count != 0)
			{
				Console.WriteLine("Adhi kaaliga ledra saami");
			}


			var dt3 = (dt2-dt1).TotalSeconds;
			Console.WriteLine("\n");
			Console.WriteLine(dt1);
			Console.WriteLine(dt2);
			if (dt3 <= 60)
				Console.WriteLine(dt3 + " Sec");
			else
			{
				var dt4 = (dt2 - dt1).TotalMinutes;
				Console.WriteLine(dt4 + " Min");
			}
			Console.WriteLine("Operator - {0}", operator_st.Count);
			Console.WriteLine("Operand - {0} ", operand_st.Count);
			Console.WriteLine("Node - {0}", node_st.Count);
			Console.WriteLine("Bracket - {0}", bracket_st.Count);
			Console.WriteLine("NodeDer - {0}", node_der.Count);
			Console.WriteLine("DerivativeResult - {0}", derivative_result.Count);
			Console.WriteLine("BracketsEncountered - {0}", brackets_encountered.Count);
			Console.WriteLine("TempStack - {0}", temp_stack.Count);
			Console.WriteLine("TempOperatorSt - {0}", temp_operator_st.Count);
			Console.WriteLine("OperandNode - {0}", operand_node.Count);
			Console.WriteLine("IntegerSt - {0}", interger_st.Count);
			Console.WriteLine("global Int is - {0}", globalint);
		}

		//--------------------------------------------------------------------------------------------------------------------------------------------//
		public static string SB(char a, int n)
		{
			string return_string;
			StringBuilder sb = new StringBuilder();
			for (int i = 0; i < n; i++)
			{
				sb.Append(a);
			}
			return_string = sb.ToString();
			return return_string;
		}

//-------------------------------------------------------------------------------------------------------------------------------------------------//
//-------------------------------------------------------------------------------------------------------------------------------------------------//
		public static bool Nullable(Node Nullable_root)
		{
			
			bool IsNullable = false;
			if (Nullable_root != null)
			{
				if (char.IsLetter(Convert.ToChar(Nullable_root.pdata)))
				{
					IsNullable = false;
				}
				if (Nullable_root.pdata == "*")
					IsNullable = true;

				if (Nullable_root.pdata == "!")
					IsNullable = true;

				if (Nullable_root.pdata == "@")
					IsNullable = false;

				if (Nullable_root.pdata == "+")
				{
					if (Nullable(Nullable_root.pfirst))
					{
						IsNullable = true;
					}
					else
					{
					   bool plus = Nullable_Auxillary_function(Nullable_root);
						if (plus)
							IsNullable = true;
						else
							IsNullable = false;
						
					}
				}

				if (Nullable_root.pdata == "~")
				{
					if (!Nullable(Nullable_root.pfirst))
					{
						IsNullable = false;
					}
					else
					{
					bool concat = Nullable_Auxillary_function(Nullable_root);
						if (concat)
							IsNullable = true;
						else
							IsNullable = false;
						
					}
				}



			}
			else
				IsNullable = true;



			return IsNullable;
		}


//----------------------------------------------------------------------------------------------------------------------------------------------//
//----------------------------------------------------------------------------------------------------------------------------------------------//
		public static bool Nullable_Auxillary_function(Node Nullable_root)
		{
			bool temp2 = false;
			bool IsNullablePlus = false;
			bool IsNullableConcat = false;
			bool IsPlusChanged = false;
			bool IsConcatChanged = false;
			bool return_value = false;

			Node temp_first = Nullable_root.pfirst;
			Node temp_next = temp_first.pnext;

			if (Nullable_root.pdata == "+")
			{
				while (temp_next != null)
				{
					temp2 = Nullable(temp_next);
					if (temp2 == true)
					{
						IsNullablePlus = true;
						IsPlusChanged = true;
					}
					temp_next = temp_next.pnext;
				}
				if (IsPlusChanged == false)
					IsNullablePlus = false;

				return_value = IsNullablePlus;
			}

			if (Nullable_root.pdata == "~")
			{
				while (temp_next != null)
				{
					temp2 = Nullable(temp_next);
					if (temp2 == false)
					{
						IsNullableConcat = false;
						IsConcatChanged = true;
					}
					temp_next = temp_next.pnext;
				}
				if (IsConcatChanged == false)
					IsNullableConcat = true;

				return_value = IsNullableConcat;
				
			}

			return return_value;
		}


//-------------------------------------------------------------------------------------------------------------------------------------------------//
//-------------------------------------------------------------------------------------------------------------------------------------------------//

		public static Node Derivative(Node Der_node, char c)
		{

			Node node_Null = new Node("@");
			Node node_empty = new Node("!");
			char Der_char = Convert.ToChar(Der_node.pdata);


			if (Der_node == null || Der_node.pdata == null)
				return node_Null;


			if (Der_node.pdata == "+")
			{
				var Der_aux_plus_node = Derivative_Auxillary_plus(Der_node, c);
				return Der_aux_plus_node;

			}

			if (Der_node.pdata == "~")
			{
				if (Nullable(Der_node.pfirst))
				{
					var Der_aux_con_node = Derivative_Auxillary_Concatenation(Der_node, c);
					return Der_aux_con_node;
				}
				else
				{
					var Der_aux_con_node = Derivative_Auxillary_Concatenation_Else(Der_node, c);
					return Der_aux_con_node;
				}

			}

			if (Der_node.pdata == "*")
			{
				Node Der_aux_star_node = Derivative_Auxillary_Star(Der_node, c);
				return Der_aux_star_node;
			}

			if (Der_char == c)
				return node_empty;

			else
				return node_Null;


		}

//-----------------------------------------------------------------------------------------------------------------------------------------------//
//-----------------------------------------------------------------------------------------------------------------------------------------------//
		public static Node Derivative_Auxillary_plus(Node Der_node, char c)
		{
			Node opr_node = new Node("+");


			var node1 = Derivative(Der_node.pfirst, c);
			Node der_next = Der_node.pfirst.pnext;

			while (der_next != null)
			{
				var node2 = Derivative(der_next, c);
				node_der.Push(node2);
				der_next = der_next.pnext;
			}

			opr_node.pfirst = node1;
			node1.pparent = opr_node;
			var temp_node1 = node1;
			while (node_der.Count != 0)
			{
				temp_node1.pnext = node_der.Pop();
				temp_node1.pnext.pparent = opr_node;
				temp_node1 = temp_node1.pnext;
			}

			return opr_node;
		}

//------------------------------------------------------------------------------------------------------------------------------------------------//
//------------------------------------------------------------------------------------------------------------------------------------------------//

		public static Node Derivative_Auxillary_Concatenation_Else(Node Der_node, char c)
		{
			Node return_node = new Node();
			var opr_node = new Node("~");

			if (Der_node.pdata == "+")
			{
				Node Deri_plus = Derivative_plus(Der_node, c);
				return_node = Deri_plus;
			}

			if (Der_node.pdata == "~")
			{
				Node Der_first_node = Der_node.pfirst;
				Node Der_next_node = new Node();
				Der_next_node = Der_node.pfirst.pnext;

				var node1 = Derivative(Der_first_node, c);
				opr_node.pfirst = node1;
				node1.pparent = opr_node;

				Node temp_first_node = opr_node.pfirst;
				while (Der_next_node != null)
				{
					temp_first_node.pnext = Der_next_node;
					Der_next_node.pparent = opr_node;

					temp_first_node = temp_first_node.pnext;
					Der_next_node = Der_next_node.pnext;
				}
				return_node = opr_node;

			}
			if (Char.IsLetter(Convert.ToChar(Der_node.pdata)) || Der_node.pdata == ".")
				return_node = Derivative_Concat_Char(Der_node, c);
			
			return return_node;

		}

//-----------------------------------------------------------------------------------------------------------------------------------------------//
//-----------------------------------------------------------------------------------------------------------------------------------------------//

		public static Node Derivative_Auxillary_Concatenation(Node Der_node, char c)
		{
			Node return_node = new Node();
			Node concat_node = new Node("~");
			Node plus_node = new Node("+");


			if (Der_node.pdata == "+")
			{
				Node Deri_plus = Derivative_plus(Der_node, c);
				return_node = Deri_plus;
			}
			if (Der_node.pdata == "~")
			{
				Node Der_first_node = Der_node.pfirst;
				Node Der_next_node = Der_node.pfirst.pnext;
				Node Derivative_next_node = new Node();
				Node Derivative_first_node = Derivative(Der_first_node, c);

				if (Nullable(Der_next_node) && Der_next_node.pnext != null)
				{
					if (Der_next_node.pdata == "*")
						Derivative_next_node = Derivative_Auxillary_Star(Der_next_node, c);

					else
						Derivative_next_node = Derivative_Auxillary_Concatenation(Der_next_node, c);

				}
				if ((!(Nullable(Der_next_node))) && Der_next_node.pnext != null)
				{
					if (Der_next_node.pdata == "*")
						Derivative_next_node = Derivative_Auxillary_Star(Der_next_node, c);
					
					else
						Derivative_next_node = Derivative_Auxillary_Concatenation_Else(Der_next_node, c);
				}

				if(Der_next_node.pnext == null)
					Derivative_next_node = Derivative(Der_next_node, c);


				concat_node.pfirst = Derivative_first_node;
				Derivative_first_node.pparent = concat_node;

				Node temp_first_node = concat_node.pfirst;
				while (Der_next_node != null)
				{
					temp_first_node.pnext = Der_next_node;
					Der_next_node.pparent = concat_node;

					temp_first_node = temp_first_node.pnext;
					Der_next_node = Der_next_node.pnext;
				}

				plus_node.pfirst = concat_node;
				concat_node.pparent = plus_node;

				concat_node.pnext = Derivative_next_node;
				Derivative_next_node.pparent = plus_node;

				return_node = plus_node;
			}

			if (Char.IsLetter(Convert.ToChar(Der_node.pdata)) || Der_node.pdata == ".")
				return_node = Derivative_Concat_Char(Der_node, c);

			return return_node;

		}


//-----------------------------------------------------------------------------------------------------------------------------------------------//
//-----------------------------------------------------------------------------------------------------------------------------------------------//
		public static Node Derivative_plus(Node Der_node, char c)
		{
			Node return_node = new Node();
			if (Der_node.pdata == "+" && Der_node.pnext != null)
			{
				Node parent_node = new Node("~");
				Node Deri_plus = Derivative(Der_node, c);

				parent_node.pfirst = Deri_plus;
				Deri_plus.pparent = parent_node;

				Node temp1 = parent_node.pfirst;
				Node temp2 = Der_node.pnext;

				while (temp2 != null)
				{
					temp1.pnext = temp2;
					temp1.pnext.pparent = parent_node;

					temp1 = temp1.pnext;
					temp2 = temp2.pnext;
				}
				return_node = parent_node;
			}

			if (Der_node.pdata == "+" && Der_node.pnext == null)
			{
				Node Deri_plus = Derivative(Der_node, c);
				return_node = Deri_plus;
			}

			return return_node;
		}


//------------------------------------------------------------------------------------------------------------------------------------------------//
//------------------------------------------------------------------------------------------------------------------------------------------------//
		public static Node Derivative_Concat_Char(Node Der_node, char c)
		{
			Node return_node = new Node();
			Node opr_node = new Node("~");
			Node else_node = Derivative(Der_node, c);
			if (Der_node.pnext != null)
			{
				opr_node.pfirst = else_node;
				else_node.pparent = opr_node;

				Node temp_first_node = opr_node.pfirst;
				Node temp = Der_node.pnext;
				while (temp != null)
				{
					temp_first_node.pnext = temp;
					temp.pparent = opr_node;

					temp = temp.pnext;
					temp_first_node = temp_first_node.pnext;
				}
				return_node = opr_node;
			}
			else
				return_node = else_node;

			return return_node;

		}
//-----------------------------------------------------------------------------------------------------------------------------------------------//
//-----------------------------------------------------------------------------------------------------------------------------------------------//

		public static Node Derivative_Auxillary_Star(Node Der_node, char c)
		{
			Node concat_node = new Node("~");
			Node derivative_node = new Node();
			Node star_node = Der_node;
			Node star_first_node = Der_node.pfirst;



			derivative_node = Derivative(Der_node.pfirst, c);

			concat_node.pfirst = derivative_node;
			derivative_node.pnext = star_node;

			star_node.pparent = concat_node;
			derivative_node.pparent = concat_node;

			return concat_node;

		}


//------------------------------------------------------------------------------------------------------------------------------------------------//
//------------------------------------------------------------------------------------------------------------------------------------------------//

		public static Node mkeps(Node deri_node)
		{
			Node return_node = new Node();

			if (deri_node.pdata == "!")
			{
				Node empty_node = new Node("()");
				return_node = empty_node;
			}

			if (deri_node.pdata == "~")
			{
				Node seq_node = new Node("Seq");
				Node temp_node = mkeps(deri_node.pfirst);
				Node result_br_node = mkeps_seq_auxillary(temp_node);

				seq_node.pfirst = result_br_node;
				result_br_node.pparent = seq_node;

				Node seq_first_node = seq_node.pfirst;
				Node deri_node_next = deri_node.pfirst.pnext;

				while (deri_node_next != null)
				{
					Node seq_result = mkeps(deri_node_next);
					Node result_br_node_2 = mkeps_seq_auxillary(seq_result);

					seq_first_node.pnext = result_br_node_2;
					result_br_node_2.pparent = seq_node;

					deri_node_next = deri_node_next.pnext;
					seq_first_node = seq_first_node.pnext;
				}

				return_node = seq_node;
			}

			if (deri_node.pdata == "+")
			{
				if (Nullable(deri_node.pfirst))
				{
					Node left_node = new Node("Left");
					return_node = mkeps_plus_auxillary(deri_node.pfirst, left_node);
				}

				else
				{
					Node right_node = new Node("Right");
					return_node = mkeps_plus_auxillary(deri_node.pfirst.pnext, right_node);
				}
			}

			if (deri_node.pdata == "*")
			{
				Node star_node = new Node("Star []");
				return_node = star_node;
			}

			return return_node;
		}

//------------------------------------------------------------------------------------------------------------------------------------------------//
//------------------------------------------------------------------------------------------------------------------------------------------------//
		public static Node mkeps_seq_auxillary(Node deri_node)
		{
			Node left_br = new Node("(");
			Node right_br = new Node(")");

			left_br.pfirst = deri_node;
			deri_node.pnext = right_br;

			deri_node.pparent = right_br.pparent = left_br;

			return left_br;
		}

//-------------------------------------------------------------------------------------------------------------------------------------------------//
//-------------------------------------------------------------------------------------------------------------------------------------------------//
		public static Node mkeps_plus_auxillary(Node deri_node, Node direct_node)
		{
			Node left_br = new Node("(");
			Node right_br = new Node(")");

			Node plus_node_result = mkeps(deri_node);
			direct_node.pfirst = left_br;
			left_br.pnext = plus_node_result;
			plus_node_result.pnext = right_br;

			left_br.pparent = plus_node_result.pparent = right_br.pparent = direct_node;

			return direct_node;

		}


//-------------------------------------------------------------------------------------------------------------------------------------------------//
//------------------------------------------------------------------------------------------------------------------------------------------------//
		public static Node Inject(Node regex, char c, Node mkeps_node)
		{
			Node return_node = new Node();

			if (mkeps_node.pdata == "()" && (regex.pdata == c.ToString() || regex.pdata == "!")) /*(char.IsLetter(Convert.ToChar(regex.pdata))))*/
			{
				string str = "Char " + c.ToString();
				Node char_node = new Node(str);
				return_node = char_node;
			}

			if (regex.pdata == "+" && mkeps_node.pdata == "Left" && mkeps_node.pfirst.pdata == "(")
			{
				Node inject_left = new Node("Left");
				Node inject_plus_left = Inject(regex.pfirst, c, mkeps_node.pfirst.pnext);
				Node inject_aux_left = inject_plus_auxillary(inject_plus_left, inject_left);

				return_node = inject_aux_left;
			}

			if (regex.pdata == "+" && mkeps_node.pdata == "Right" && mkeps_node.pfirst.pdata == "(")
			{
				Node inject_right = new Node("Right");
				Node inject_plus_right = Inject(regex.pfirst.pnext, c, mkeps_node.pfirst.pnext);
				Node inject_aux_right = inject_plus_auxillary(inject_plus_right, inject_right);

				return_node = inject_aux_right;
			}

			if (regex.pdata == "~" && mkeps_node.pdata == "Seq")
			{



				Node r1 = regex.pfirst;
				Node v1 = mkeps_node.pfirst.pfirst;
				Node v2 = mkeps_node.pfirst.pnext.pfirst;

				return_node = inject_left_seq_auxillary(r1, v1, v2, c);
					
			}

			if (regex.pdata == "~" && mkeps_node.pdata == "Left" && mkeps_node.pfirst.pnext.pdata == "Seq")
			{
				Node seq = mkeps_node.pfirst.pnext;
				Node r1 = regex.pfirst;
				Node v1 = seq.pfirst.pfirst;
				//Node v2 = seq.pfirst.pnext.pfirst;
				Node v2 = seq.pfirst.pfirst;

				return_node = inject_left_seq_auxillary(r1, v1, v2, c);

			}

			if (regex.pdata == "~" && mkeps_node.pdata == "Right")
			{
				Node seq = new Node("Seq");
				Node v2 = mkeps_node.pfirst.pnext;
				Node r1 = regex.pfirst;

				Node mkeps_r1 = mkeps(r1);
				Node mkeps_r1_br = mkeps_seq_auxillary(mkeps_r1);

				seq.pfirst = mkeps_r1_br;
				mkeps_r1_br.pparent = seq;

				Node regex_temp = regex.pfirst.pnext;
				Node seq_pfirst = seq.pfirst;
				while (regex_temp != null)
				{
					Node br = mkeps_seq_auxillary( Inject(regex_temp, c, v2));
					seq_pfirst.pnext = br;
					br.pparent = seq;

					seq_pfirst = seq_pfirst.pnext;
					regex_temp = regex_temp.pnext;
				}

				return_node = seq;

			}

			if (regex.pdata == "*" && mkeps_node.pdata == "Seq")
			{
				Node r_node = regex.pfirst;
				Node v_node = mkeps_node.pfirst.pfirst;
				Node star_node = new Node("Stars");
				Node inject_result = Inject(r_node, c, v_node);
				return_node = inject_plus_auxillary(inject_result, star_node);
			}




			return return_node;
		}

//-----------------------------------------------------------------------------------------------------------------------------------------------//
//-----------------------------------------------------------------------------------------------------------------------------------------------//
		public static Node inject_plus_auxillary(Node inject_node, Node direct_node)
		{
			Node left_br = new Node("(");
			Node right_br = new Node(")");

			direct_node.pfirst = left_br;
			left_br.pnext = inject_node;
			inject_node.pnext = right_br;

			left_br.pparent = inject_node.pparent = right_br.pparent = direct_node;

			return direct_node;
		}

//------------------------------------------------------------------------------------------------------------------------------------------------//
//------------------------------------------------------------------------------------------------------------------------------------------------//
		public static Node inject_left_seq_auxillary(Node r1, Node v1, Node v2, char c)
		{
			Node left_br = new Node("(");
			Node right_br = new Node(")");
			Node inject_seq = new Node("Seq");
			Node inject_seq_aux = Inject(r1, c, v1);

			inject_seq.pfirst = left_br;
			left_br.pfirst = inject_seq_aux;
			inject_seq_aux.pnext = right_br;

			inject_seq_aux.pparent = right_br.pparent = left_br;
			left_br.pparent = inject_seq;

			left_br.pnext = v2;
			v2.pparent = inject_seq;

			return inject_seq;
		}

//-----------------------------------------------------------------------------------------------------------------------------------------------//
//-----------------------------------------------------------------------------------------------------------------------------------------------//
		public static string String_Checker_fucntion(string regex)
		{
			string return_node;
			int first_int = -1;
			int second_int = -1;
			char first_char = '.';
			char second_char = ',';

			if (regex.Length > 1)
			{
				int i = regex.Length - 1;
				if (i == regex.Length - 1 && regex[i] == ')')
				{
					StringBuilder int_sb = new StringBuilder();
					for (int h = i - 1; h >= 0; h--)
					{
						if (char.IsDigit(regex[h]))
							int_sb.Append(regex[h].ToString());


						if ((!char.IsDigit(regex[h]) && regex[h] != '('))
							break;

						if (regex[h] == '(')
						{
							string sb = int_sb.ToString();
							char[] arr = sb.ToCharArray();
							Array.Reverse(arr);
							StringBuilder sb2 = new StringBuilder();
							foreach (char c in arr)
							{
								sb2.Append(c.ToString());
							}
							string sb1 = sb2.ToString();
							second_int = Convert.ToInt32(sb1);
							if (char.IsLetter(regex[h - 1]))
								second_char = regex[h - 1];
						}
					}
				}

				int j = 0;
				if (char.IsLetter(regex[j]) && j == 0 && regex[j + 1] == '?' && regex[j + 2] == '(')
				{
					first_char = regex[j];
					StringBuilder int_sb = new StringBuilder();
					for (int k = j + 3; k <= regex.Length - 1; k++)
					{
						if (char.IsDigit(regex[k]))
							int_sb.Append(regex[k].ToString());


						if ((!char.IsDigit(regex[k]) && regex[k] != ')'))
							break;

						if (regex[k] == ')')
						{
							string sb = int_sb.ToString();
							first_int = Convert.ToInt32(sb);

						}

					}
				}
			}

			if (first_int == second_int && first_char == second_char)
			{
				globalchar = first_char;
				string str = first_char.ToString();
				string empty = "!";

				string str2 = "(" + str + "+" + empty + ")";
				StringBuilder sb = new StringBuilder();
				for (int k = 0; k <= first_int-1; k++)
				{
					sb.Append(str2);
				}

				StringBuilder sb2 = new StringBuilder();
				for (int l = 0; l <= first_int-1; l++)
				{
					sb2.Append(second_char);
				}

				string first_str = "(" + sb.ToString() + ")";
				string second_str = "(" + sb2.ToString() + ")";

				string original = first_str + second_str;

				 return_node = original;
			}
			else
			{
				return_node = regex;
			}

			globalint = first_int;
			return return_node;

		}


//----------------------------------------------------------------------------------------------------------------------------------------------------//
//--------------------------------------------------------------------------------------------------------------------------------------------------//
		public static Node ConstructTree(string regex)
		{
			int no_of_characters_scanned = 0;
			for (int i = regex.Length - 1; i >= 0; i--)
			{
				
				no_of_characters_scanned++;
				if (regex[i] == ')')
				{
					if (i != regex.Length - 1)
					{
						if (operator_st.Count == 0 && operand_st.Count != 0)
						{
							Concatenate_operand_and_node_stack_elements_0_operators();
							while (node_st.Count != 0)
							{
								temp_stack.Push(node_st.Pop());
							}
						}

						if (char.IsLetter(regex[i + 1]))
						{
							Construttree_Auxillary();
							while (node_st.Count != 0)
							{
								temp_stack.Push(node_st.Pop());
							}
						}

						if ((char.IsSymbol(regex[i + 1]) || regex[i + 1] == '*') && operator_st.Count !=0  )
						{
							char opr = operator_st.Pop();
							if (opr == regex[i + 1] && operator_st.Count == 0 && opr != '*')
							{
								temp_operator_st.Push(opr);
								Concatenating_operands();
								Concatenate_nodestack_elements_when_0_operands_and_operators();
								temp_stack.Push(node_st.Pop());
							}

							if (opr == regex[i + 1] && opr == '*')
							{
								Construttree_Auxillary();
								if (node_st.Count != 0)
								{
									temp_stack.Push(node_st.Pop());
								}
								operator_st.Push(opr);
							}
						}
					}

					bracket_st.Push(regex[i].ToString());
				}


				if (regex[i] == '(')
				{
					
						string br = bracket_st.Pop();
					if (br == ")")
					{
						if (bracket_st.Count != 0)
						{
							if (bracket_st.Pop() == ")")
							{
								bracket_st.Push(")");
								bracket_st.Push(")");
								When_plus_encountered_and_contents_are_there_in_operand_stack();
								//Concatenating_operands();
								Construttree_Auxillary();
								bracket_st.Pop();
							}
						}

						else
						{
								Construttree_Auxillary();
								brackets_encountered.Push(node_st.Pop());
						}
					}
						
					

				}


				if (char.IsLetter(regex[i]) || (regex[i] != '+' && regex[i] != '~' && regex[i] != '*' && regex[i] != '(' && regex[i] != ')'))
					operand_st.Push(regex[i].ToString());


				if (regex[i] == '+' || regex[i] == '~'|| regex[i] == '*')
				{
					if (regex[i] == '*' && regex[i - 1] != ')' && char.IsLetter(regex[i - 1]))
					{
						Node node1 = new Node(regex[i].ToString());
						Node node2 = new Node(regex[i - 1].ToString());

						node1.pfirst = node2;
						node2.pparent = node1;
						node_st.Push(node1);
						i = i - 1;
						no_of_characters_scanned = no_of_characters_scanned + 1;
					}
					else
					{
						if (bracket_st.Count < 2)
						{
							if (operator_st.Count == 0)
							{
								if (node_st.Count == 0)
									Concatenating_operands();
								else
									Concatenating_operands_in_operand_node_stack_together();

							}
							else
								Dealing_when_Operator_encountered();
						}

						if (bracket_st.Count >= 2)
						{
							concatenating_contents_of_the_operand_st_and_pushing_onto_operand_stack();
						}

							operator_st.Push(regex[i]);
						

					}

				}

			}


			if (no_of_characters_scanned == regex.Length && (operand_st.Count != 0 || operator_st.Count != 0 || node_st.Count != 0))
			{
				if (bracket_st.Count != 0)
				{
					if (node_st.Count != 0)
					{
						while (node_st.Count == 0)
						{
							node_st.Pop();
						}
					}
					return null;
				}

				if (operator_st.Count == 1 && operand_st.Count >= 1 && brackets_encountered.Count >= 1)
				{
					Concatenating_operands();
					if (node_st.Count > 1)
						Concatenate_nodestack_elements_when_0_operands_and_operators();

					Count_of_all_stacks_are_0_excepts_brackets_encountered_stack();

					char opr = operator_st.Pop();
					Node parent_node = new Node(opr.ToString());
					Node next = node_st.Pop();
					Node first = node_st.Pop();

					parent_node.pfirst = first;
					first.pnext = next;

					first.pparent = next.pparent = parent_node;
					node_st.Push(parent_node);

				}

				else
				{
					Construttree_Auxillary();
				}


			}

			if (operand_st.Count == 0 && operator_st.Count == 0)
			{
				if (node_st.Count == 0)
				{
					if (temp_stack.Count == 0 && temp_operator_st.Count == 0 && brackets_encountered.Count != 0)
					{
						Count_of_all_stacks_are_0_excepts_brackets_encountered_stack();
					}

					if (temp_stack.Count == 1 && temp_operator_st.Count == 1 && brackets_encountered.Count == 1)
					{
						char opr = temp_operator_st.Pop();
						Node parent_node = new Node(opr.ToString());
						joining_elements_in_temporary_operator_and_brackets_encountered_stacks(parent_node);
					}

					if (temp_stack.Count == 1 && temp_operator_st.Count == 0 && brackets_encountered.Count == 1)
					{
						Node parent_node = new Node("~");
						joining_elements_in_temporary_operator_and_brackets_encountered_stacks(parent_node);
					}
				}
				else
				{
					if (node_st.Count == 1 && brackets_encountered.Count == 1)
					{
						Node parent_node = new Node("~");
						Node first = node_st.Pop();
						Node next = brackets_encountered.Pop();

						parent_node.pfirst = first;
						first.pnext = next;
						first.pparent = next.pparent = parent_node;

						node_st.Push(parent_node);

					}

				}
					
			}
			Node main_root = node_st.Pop();
			//Console.WriteLine("Contents of all stacks are : oprand - {0} , operator-{1}, node-{2}, br-{3}, brack-{4}", operand_st.Count, operator_st.Count, node_st.Count, bracket_st.Count, brackets_encountered.Count);
			return main_root;

		}

//-----------------------------------------------------------------------------------------------------------------------------------------------//
//-----------------------------------------------------------------------------------------------------------------------------------------------//
		public static void joining_elements_in_temporary_operator_and_brackets_encountered_stacks(Node parent_node)
		{
			Node first = brackets_encountered.Pop();
			Node next = temp_stack.Pop();

			parent_node.pfirst = first;
			first.pnext = next;

			first.pparent = next.pparent = parent_node;

			node_st.Push(parent_node);
		}

//-------------------------------------------------------------------------------------------------------------------------------------------------//
//-------------------------------------------------------------------------------------------------------------------------------------------------//
		public static void Construttree_Auxillary()
		{
			if (bracket_st.Count < 2)
			{
				if (operator_st.Count == 0)
				{

					if (operand_st.Count != 0 && node_st.Count == 0 /*&& operator_st.Count == 0*/)
						Concatenating_operands();

					if (operand_st.Count != 0 /*&& operator_st.Count == 0*/ && node_st.Count != 0)
						Concatenate_operand_and_node_stack_elements_0_operators();

					if (operand_st.Count == 0 /*&& operator_st.Count == 0 */&& node_st.Count != 0)
					{
						if (node_st.Count >= 2)
							Concatenate_nodestack_elements_when_0_operands_and_operators();

					}

				}
				else
					when_star_or_other_operator_encountered_and_it_is_last();
			}
			


		}

//------------------------------------------------------------------------------------------------------------------------------------------------//
//------------------------------------------------------------------------------------------------------------------------------------------------//
		public static void When_plus_encountered_and_contents_are_there_in_operand_stack()
		{
			if (operator_st.Count == 1)
			{
				char opr = operator_st.Pop();
				if (opr == '+')
				{
					concatenating_contents_of_the_operand_st_and_pushing_onto_operand_stack();
					if (operand_node.Count == 2)
					{
						Node parent_node = new Node(opr.ToString());
						Node node1 = operand_node.Pop();
						Node node2 = operand_node.Pop();

						parent_node.pfirst = node1;
						node1.pnext = node2;

						node1.pparent = node2.pparent = parent_node;

						node_st.Push(parent_node);
					}
				}
			}
		}

//------------------------------------------------------------------------------------------------------------------------------------------------//
//------------------------------------------------------------------------------------------------------------------------------------------------//
		public static void concatenating_contents_of_the_operand_st_and_pushing_onto_operand_stack()
		{

			int first_node = 0;

			if (operand_st.Count == 0)
				return;

			if (operand_st.Count == 1)
			{
				string operand = operand_st.Pop();
				Node str = new Node(operand);
				operand_node.Push(str);
				return;
			}

			if (operand_st.Count >= 2)
			{
				Node parent_operator = new Node("~");
				while (operand_st.Count != 0)
				{
					string operand = operand_st.Pop();
					Node str = new Node(operand);
					if (first_node == 0 && parent_operator.pfirst == null)
					{
						parent_operator.pfirst = str;
						str.pparent = parent_operator;
						first_node++;
					}
					else
					{
						Node temp_node = parent_operator.pfirst;
						while (temp_node.pnext != null)
						{
							temp_node = temp_node.pnext;
						}
						temp_node.pnext = str;
						str.pparent = parent_operator;
					}
				}
				operand_node.Push(parent_operator);
			}


		}
//---------------------------------------------------------------------------------------------------------------------------------------------//
//---------------------------------------------------------------------------------------------------------------------------------------------//
		public static void when_star_or_other_operator_encountered_and_it_is_last()
		{
			while (operator_st.Count != 0)
			{
				char operator1 = operator_st.Pop();
				if (operator_st.Count == 0)
				{
					if (operator1 == '*' && operand_st.Count == 0)
						Star_operator_encountered(operator1);
					if (operator1 == '*' && operand_st.Count != 0)
					{
						Concatenating_operands();
						Star_operator_encountered(operator1);
					}
				}

				if (operator_st.Count == 0 && operator1 != '*')
				{
					Concatenating_operands();
					if (node_st.Count >= 2)
					{
						Node parent_operator = new Node(operator1.ToString());
						Node node1 = node_st.Pop();
						Node node2 = node_st.Pop();

						node1.pnext = node2;
						parent_operator.pfirst = node1;
						node1.pparent = node2.pparent = parent_operator;
						node_st.Push(parent_operator);
					}



				}


				if (operator_st.Count != 0)
				{
					operator_st.Push(operator1);
					Dealing_when_Operator_encountered();
				}
			}
		}

//-------------------------------------------------------------------------------------------------------------------------------------------------//
//-------------------------------------------------------------------------------------------------------------------------------------------------//

		public static void Star_operator_encountered(char operator1)
		{
			Node main_root2 = new Node(operator1.ToString());
			if (node_st.Count == 1)
			{
				Node last_node = node_st.Pop();
				main_root2.pfirst = last_node;
				last_node.pparent = main_root2;
				node_st.Push(main_root2);
			}
			else
			{
				Concatenate_nodestack_elements_when_0_operands_and_operators();
				Node last_node = node_st.Pop();
				main_root2.pfirst = last_node;
				last_node.pparent = main_root2;
				node_st.Push(main_root2);

			}
		}

//-----------------------------------------------------------------------------------------------------------------------------------------------//
//-----------------------------------------------------------------------------------------------------------------------------------------------//

		public static void Concatenating_operands()
		{
			int first_node = 0;

			if (operand_st.Count == 0)
				return;

			if (operand_st.Count == 1)
			{
				string operand = operand_st.Pop();
				Node str = new Node(operand);
				node_st.Push(str);
				return;
			}

			if (operand_st.Count >= 2)
			{
				Node parent_operator = new Node("~");
				while (operand_st.Count != 0)
				{
					string operand = operand_st.Pop();
					Node str = new Node(operand);
					if (first_node == 0 && parent_operator.pfirst == null)
					{
						parent_operator.pfirst = str;
						str.pparent = parent_operator;
						first_node++;
					}
					else
					{
						Node temp_node = parent_operator.pfirst;
						while (temp_node.pnext != null)
						{
							temp_node = temp_node.pnext;
						}
						temp_node.pnext = str;
						str.pparent = parent_operator;
					}
				}
				node_st.Push(parent_operator);
			}
		}

//------------------------------------------------------------------------------------------------------------------------------------------------//
//------------------------------------------------------------------------------------------------------------------------------------------------//

		public static void Concatenating_operands_in_operand_node_stack_together()
		{
			while (operand_st.Count != 0)
			{
				Node node1 = new Node(operand_st.Pop());
				node_st.Push(node1);
			}
			if (operand_st.Count == 0 && node_st.Count != 0)
				Concatenate_nodestack_elements_when_0_operands_and_operators();
		}

//-------------------------------------------------------------------------------------------------------------------------------------------------//
//------------------------------------------------------------------------------------------------------------------------------------------------//

		public static void Concatenate_nodestack_elements_when_0_operands_and_operators()
		{
			int first_node = 0;
			Node parent_node = new Node("~");
			if (node_st.Count > 1)
			{
				while (node_st.Count != 0)
				{
					Node str = node_st.Pop();

					if (first_node == 0 && parent_node.pfirst == null)
					{
						parent_node.pfirst = str;
						str.pparent = parent_node;
						first_node++;
					}
					else
					{
						Node temp_node = parent_node.pfirst;
						while (temp_node.pnext != null)
						{
							temp_node = temp_node.pnext;
						}
						temp_node.pnext = str;
						str.pparent = parent_node;
					}
				}
				node_st.Push(parent_node);
			}


		}

//----------------------------------------------------------------------------------------------------------------------------------------------//
//----------------------------------------------------------------------------------------------------------------------------------------------//

		public static void Concatenate_operand_and_node_stack_elements_0_operators()
		{
			Node parent_node = new Node("~");
			while (operand_st.Count != 0)
			{
				Node temp_node = new Node(operand_st.Pop());
				if (parent_node.pfirst == null)
				{
					parent_node.pfirst = temp_node;
					temp_node.pparent = parent_node;
				}
				else
				{
					Node next_node = parent_node.pfirst;
					while (next_node.pnext != null)
					{
						next_node = next_node.pnext;
					}
					next_node.pnext = temp_node;
					temp_node.pparent = parent_node;
				}

			}

			while (node_st.Count != 0)
			{
				Node str = node_st.Pop();
				Node temp_node = parent_node.pfirst;
				while (temp_node.pnext != null)
				{
					temp_node = temp_node.pnext;
				}
				temp_node.pnext = str;
				str.pparent = parent_node;

			}

			node_st.Push(parent_node);
		}

//-----------------------------------------------------------------------------------------------------------------------------------------------//
//-----------------------------------------------------------------------------------------------------------------------------------------------//

		public static void Dealing_when_Operator_encountered()
		{
			Concatenating_operands();

			char operatorr = operator_st.Pop();
			if (operatorr == '*')
			{
				operator_st.Push(operatorr);
				return;
			}

			if (node_st.Count >= 2)
			{
				Node parent_operator = new Node(operatorr.ToString());
				Node node1 = node_st.Pop();
				Node node2 = node_st.Pop();

				node1.pnext = node2;
				parent_operator.pfirst = node1;
				node1.pparent = node2.pparent = parent_operator;
				node_st.Push(parent_operator);
			}
		}

//-----------------------------------------------------------------------------------------------------------------------------------------------//
//-----------------------------------------------------------------------------------------------------------------------------------------------//

		public static void Count_of_all_stacks_are_0_excepts_brackets_encountered_stack()
		{
			Node parent_node = new Node("~");
			if (brackets_encountered.Count > 1)
			{
				while (brackets_encountered.Count != 0)
				{
					Node br_node = brackets_encountered.Pop();
					if (parent_node.pfirst == null)
					{
						parent_node.pfirst = br_node;
						br_node.pparent = parent_node;
					}
					else
					{
						Node temp = parent_node.pfirst;
						while (temp.pnext != null)
						{
							temp = temp.pnext;
						}
						temp.pnext = br_node;
						br_node.pparent = parent_node;
					}
				}
				node_st.Push(parent_node);
			}
			else
			{
				node_st.Push(brackets_encountered.Pop());
			}


		}

//------------------------------------------------------------------------------------------------------------------------------------------------//
//------------------------------------------------------------------------------------------------------------------------------------------------//
		public static void Display_Contents_of_the_node(Node root_node)
		{
			Queue<Node> st = new Queue<Node>();
			st.Enqueue(root_node);
			while (st.Count != 0)
			{
				Node temp_Dfs_node = st.Dequeue();
				if (temp_Dfs_node.pparent != null)
					Console.WriteLine("\tthe data is {0} and its parent is {1} ", temp_Dfs_node.pdata, temp_Dfs_node.pparent.pdata);
				else
					Console.WriteLine("The root value is {0} ", temp_Dfs_node.pdata);

				if (temp_Dfs_node.pfirst != null)
				{
					st.Enqueue(temp_Dfs_node.pfirst);
					Node next_node = temp_Dfs_node.pfirst;
					while (next_node.pnext != null)
					{
						st.Enqueue(next_node.pnext);
						next_node = next_node.pnext;
					}
				}
			}
		}

//-----------------------------------------------------------------------------------------------------------------------------------------------//
//-----------------------------------------------------------------------------------------------------------------------------------------------//
		public static void Display_Contents_of_mkeps_node(Node mkeps_node)
		{
			if (mkeps_node != null)
			{
				Console.Write(mkeps_node.pdata);
				Display_Contents_of_mkeps_node(mkeps_node.pfirst);
				Display_Contents_of_mkeps_node(mkeps_node.pnext);
			}
		}


	}
}
