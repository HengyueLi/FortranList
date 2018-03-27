# FortranList

A general list structure of Fortran2003. One can define a general data type.

It is similar to a one dimensional array. But one need not to give the length at the declaration.

It is suitable to store large quantities of values.

## example:


!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!        example start

! to define a data type
      module DataModule
              implicit none
              type::IntegerType
                   integer::i
              endtype
      endmodule

! define the corresponding list
      module IntegerList
              use DataModule,  data => IntegerType
              !implicit none   do not add this statement. It is contained in 'ListStructure.finc'
              include "ListStructure.finc"
      endmodule






      program main
             use IntegerList ,only: List => ListStru , IntegerType => data
             implicit none
             !-------------declaration
             class(IntegerType),pointer::dat
             type(List)::l

             integer::i
             integer::c(3:5)

             !-------------Initialization    (needed)
             call l%Initialization()

             !------------append 1 to 100
             do i = 1 , 5
               allocate(dat)
               dat%i = i
               ! append will 'absorb' the dat into the list.
               ! After the absorbtion, dat becomes unallocated.
               ! So in the next iteration, we can allocate dat again.
               call l%append(dat)
             enddo


             !---------show the length of list----------------
             write(*,*)"The length of list is :",l%GetLen()

             !---------use a pointer to obtain a data
             if (associated(dat)) deallocate(dat)
             dat => l%GetDataPointer(i=2)
             write(*,*)"the value at i=2 is:",dat%i

             !--------fast get the values of a segment of list from i=3 to i=5
             call l%SetMark(i=3)
             do i = 3 , 5
               dat => l%GetMarkedPointerAndNext()
               c(i) = dat%i
             enddo
             write(*,*)c




             !there are some other methods. One can find that in source code.


     endprogram main




     !        example end   
     !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
