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
			pdata = null;
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






	class MainClass
	{
 //--------------------Four stacks. 1) Operator stack, stores operators as characters
		// ---------------2) Operand stack, stores operands as strings 
		//----------------3) Bracket stack, to check for equal no of brackets in the regular expression
		//----------------4) Node stack, which contains operands and operators concatenated and stored as node type----------//

		public static Stack<char> operator_st = new Stack<char>();
		public static Stack<string> operand_st = new Stack<string>();
		public static Stack<string> bracket_st = new Stack<string>();
		public static Stack<Node> node_st = new Stack<Node>();
		public static bool temp1 = false;
		public static bool temp2 = false;


		public static void Main(string[] args)
		{

			Console.WriteLine("\nEnter your regular expression");
			string stregex = Console.ReadLine();

			//Console.WriteLine("\nEnter your string");
			//string str = Console.ReadLine();



			Node root = ConstructTree(stregex);
			if (root == null)
				Console.WriteLine("\nPlease enter valid regular expression");

			else
			{
				Display_Contents_of_the_node(root);
				Console.WriteLine(Nullable(root));
			}

		}



		public static bool Nullable(Node Nullable_root)
		{
			
			bool IsNullable = false;
			if (Nullable_root != null)
			{
				if (Nullable_root.pdata == "*")
					IsNullable = true;
				



				if (Nullable_root.pdata == "+")
				{
					Nullable_Auxillary_function(Nullable_root);
					if (temp2 == true || temp1 == true)
						IsNullable = true;
					if (temp1 == false && temp2 == false)
						IsNullable = false;
				}

				if (Nullable_root.pdata == "~")
				{
					Nullable_Auxillary_function(Nullable_root);
					if (temp2 == false || temp1 == false)
						IsNullable = false;
					if (temp1 == true && temp2 == true)
						IsNullable = true;
				}

			}
			else
				IsNullable = true;
		


			return IsNullable;
		}


		public static void Nullable_Auxillary_function(Node Nullable_root)
		{
			Node temp_first = Nullable_root.pfirst;

			temp1 = Nullable(temp_first);
			Node temp_next = temp_first.pnext;
			while (temp_next != null)
			{
				temp2 = Nullable(temp_next);
				temp_next = temp_next.pnext;
			}
		}
	//---------------This method reads the string(regular expression) in reverse order and calls various other functions.
    //                 The return value of this function is a root node of the tree.--------------------------------------//

		public static Node ConstructTree(string regex)
		{
			int no_of_characters_scanned = 0;	
			for (int i = regex.Length - 1; i >= 0; i--)
			{
				no_of_characters_scanned++;
				if (regex[i] == ')')
					bracket_st.Push(regex[i].ToString());
				if (regex[i] == '(')
				{
					bracket_st.Pop();
					/*if (bracket_st.Count == 0)
						return null;
					else
					{
						string br = bracket_st.Pop();
						if (br == ")")
						{
							if (operator_st.Count == 0 && i!= 0)
							{
								Concatenating_operands();
							}
							char operator_star = operator_st.Pop();
							if(operator_star == '*')
								Star_operator_encountered(operator_star);
							
						}
					}*/

				}

				//------------------if the read character is an alphabet, pust that into operand stack-------------------//
				if (char.IsLetter(regex[i]))
					operand_st.Push(regex[i].ToString());

      
        //------------------if the read character is a symbol, first check whehter there are any other symbols in
        //                     operator stack. if the count of number of symbols in operator stack is 0, if there are
        //                        any operands in the operand stack that needs to concatenated. if yes, then call 
		//                           Concatenating_operands method to concatenated them.
        //------------------if there are already any other symbols in the operator stack, before pushing the present symbol
        //                     into the operator stack, call Dealing_when_Operator_encountered method and then push the
        //                       present symbol into the stack ----------------------------------------------------------------//

				if (char.IsSymbol(regex[i]) || regex[i] == '*')
				{
					if (regex[i] == '*' && regex[i - 1] != ')' && char.IsLetter(regex[i-1]))
					{
						Node node1 = new Node(regex[i].ToString());
						Node node2 = new Node(regex[i - 1].ToString());

						node1.pfirst = node2;
						node2.pparent = node1;
						node_st.Push(node1);
						i = i - 1;
						no_of_characters_scanned = no_of_characters_scanned+1;
					}
					else
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

						operator_st.Push(regex[i]);
					}
				}

			}


			if (no_of_characters_scanned == regex.Length && (operand_st.Count != 0 || operator_st.Count != 0 || node_st.Count!=0))
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
				Construttree_Auxillary();
			}

			Node main_root = node_st.Pop();
			return main_root;

		}



		public static void Construttree_Auxillary()
		{
			
			if (operand_st.Count != 0 && node_st.Count == 0 && operator_st.Count == 0)
				Concatenating_operands();

			if (operand_st.Count != 0 && operator_st.Count == 0 && node_st.Count != 0)
				Concatenate_operand_and_node_stack_elements_0_operators();

			if (operand_st.Count == 0 && operator_st.Count == 0 && node_st.Count != 0)
			{
				if (node_st.Count >= 2)
					Concatenate_nodestack_elements_when_0_operands_and_operators();

			}

			if (operand_st.Count == 0 & operator_st.Count != 0 && node_st.Count != 0)
			{

			}


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
					Node parent_operator = new Node(operator1.ToString());
					Node node1 = node_st.Pop();
					Node node2 = node_st.Pop();

					node1.pnext = node2;
					parent_operator.pfirst = node1;
					node1.pparent = node2.pparent = parent_operator;
					node_st.Push(parent_operator);

				}


				if (operator_st.Count != 0)
				{
					operator_st.Push(operator1);
					Dealing_when_Operator_encountered();
				}
			}

		}



		public static void Star_operator_encountered(char operator1)
		{
			Node main_root2 = new Node(operator1.ToString());
			Node last_node = node_st.Pop();
			main_root2.pfirst = last_node;
			last_node.pparent = main_root2;
			node_st.Push(main_root2);
		}


    //----------------This method is used to read operands from the operand stack, conacatenates all the operands
    //                  in case if there are more than one operand in the stack and push concatenated operands as one
    //                    node into the node stack  ------------------------------------------------------------------//
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



    //-------------This method concatenates contents of operand stack and node stack together when there is one operator
		//---------------------------------------------------------------------------------------------------------------//


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




		//--------------This method concatenates node stack elements when there are no operands and operators-----------------//
		//----------------------------------------------------------------------------------------------------------------//


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



	//-------------------This method concatenates contents of operand stack and node stack only when there are 0 operators
		//---------------------------------------------------------------------------------------------------------------//


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



 //---------This method is called when there are contents in operator stack and the present character read in the string
 //            is a operator. First it checks whether are there any contents in the operand stack, if yes concatenate them
 //               and adds that node to the node stack. Then performs create a node with the top most element of the operator 
 //          		stack as parent and top two elements of the node stack as children. After creating this node, it again pushed
 //                   back into the node stack ------------------------------------------------------------------------------------//


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



 //-----------This method is used to display the contents of the tree formed as per BFS traversal-------------------------------//


		public static void Display_Contents_of_the_node(Node root_node)
		{
			Queue<Node> st = new Queue<Node>();
			st.Enqueue(root_node);
			while (st.Count != 0)
			{
				Node temp_Dfs_node = st.Dequeue();
				Console.WriteLine("\t{0}", temp_Dfs_node.pdata);
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


	}
}
