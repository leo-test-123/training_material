----------------
Genericity Lab
----------------

* Requirements

   - Create a list ADT to hold any type of data

      - Operations should include adding to the list and sorting the list

   - Create a record structure containing multiple fields
   - The `main` program should:

      + Allow the addition of multiple records into the list
      + Sort the list
      + Print the list

* Hints

   - Sort routine will need to know how to compare elements

------------------------------------------
Genericity Lab Solution - Generic (Spec)
------------------------------------------

.. code:: Ada

   generic
     type Element_T is private;
     Max_Size : Natural;
     with function "<" (L, R : Element_T) return Boolean is <>;
   package Generic_List is

     type List_T is tagged private;

     procedure Add (This : in out List_T;
                    Item : in     Element_T);

     procedure Sort (This : in out List_T);

   private

     subtype Index_T is Natural range 0 .. Max_Size;
     type List_Array_T is array (1 .. Index_T'Last) of Element_T;

     type List_T is tagged record
       Values : List_Array_T;
       Length : Index_T := 0;
     end record;

   end Generic_List;

------------------------------------------
Genericity Lab Solution - Generic (Body)
------------------------------------------

.. code:: Ada

   package body Generic_List is

     procedure Add
      (This : in out List_T;
       Item : in     Element_T) is
     begin
       This.Length               := This.Length + 1;
       This.Values (This.Length) := Item;
     end Add;

     procedure Sort (This : in out List_T) is
       Temp : Element_T;
     begin
       for I in 1 .. This.Length loop
         for J in I + 1 .. This.Length loop
           if This.Values (J) < This.Values (J - 1)
           then
             Temp                := This.Values (J);
             This.Values (J)     := This.Values (J - 1);
             This.Values (J - 1) := Temp;
           end if;
         end loop;
       end loop;
     end Sort;

   end Generic_List;

------------------------------------------
Genericity Lab Solution - Generic Print
------------------------------------------

.. code:: Ada

   generic
     with function Image (Element : Element_T) return String;
   package Generic_List.Output is
     procedure Print (List : List_T);
   end Generic_List.Output;

   with Ada.Text_IO; use Ada.Text_IO;
   package body Generic_List.Output is
     procedure Print (List : List_T) is
     begin
       for I in 1 .. List.Length loop
         Put_Line (Integer'Image (I) & ") " &
                   Image (List.Values (I)));
       end loop;
     end Print;
   end Generic_List.Output;

-----------------------------------
Genericity Lab Solution - Main
-----------------------------------

.. code:: Ada

   with Ada.Text_IO; use Ada.Text_IO;
   with Data_Type;
   with Generic_List;
   with Generic_List.Output;
   use type Data_Type.Record_T;
   procedure Main is
     package List is new Generic_List (Data_Type.Record_T, 10);
     package Output is new List.Output (Data_Type.Image);
     My_List : List.List_T;
     Element : Data_Type.Record_T;
   begin
     loop
       Put ("Enter character: ");
       declare
         Str : constant String := Get_Line;
       begin
         exit when Str'Length = 0;
         Element.Field2 := Str (1);
       end;
       Put ("Enter number: ");
       declare
         Str : constant String := Get_Line;
       begin
         exit when Str'Length = 0;
         Element.Field1 := Integer'Value (Str);
       end;
       My_List.Add (Element);
     end loop;
     My_List.Sort;
     Output.Print (My_List);
   end Main;

